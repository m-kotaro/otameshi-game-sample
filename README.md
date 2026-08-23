# otameshi-game-sample

Game Plaza プラットフォームに載せる外部ゲームのテンプレートリポジトリ。  
このリポジトリをフォークして、ゲームを実装する。

## 構成

```
index.html   ... ゲーム本体（HTML + CSS + JS の単一ファイル）
meta.json    ... ゲームのメタ情報（プラットフォームが読み取る）
```

## 実装ガイド（AI向け）

このテンプレートを使ってゲームを実装する際のルール。

### やること

1. `index.html` 内の「ゲームロジック」セクションにゲームを実装する
2. `meta.json` のタイトル・説明文をゲームに合わせて更新する

### やらなくていいこと

- `GamePlazaSDK` の実装は変更不要（そのまま使う）
- ビルドツール・バンドラーは不要（単一HTMLで完結）

---

## プラットフォーム連携仕様

`index.html` に埋め込み済みの `GamePlazaSDK` がプラットフォームとの通信を担う。  
ゲームロジックからは以下の API を呼ぶだけでよい。

### ライフサイクル

1. ゲームが iframe で読み込まれる
2. プラットフォームから `game_start` メッセージが届く → `GamePlazaSDK.init` のコールバックが発火
3. ゲーム終了時に `GamePlazaSDK.sendResult()` または `GamePlazaSDK.sendClose()` を呼ぶ

### API

#### `GamePlazaSDK.init(callback)`

ゲーム開始時のコールバックを登録する。

```js
GamePlazaSDK.init(({ gameType, players, localPlayer }) => {
  // gameType: string - ゲーム識別子
  // players: Array<{ userName: string, uuid: string, isLocal: boolean }>
  // localPlayer: { userName, uuid, isLocal: true } - 操作しているプレイヤー
});
```

#### `GamePlazaSDK.sendResult(winnerId, scores?)`

ゲーム結果を送信する。呼ぶとプラットフォーム側でゲーム終了として扱われる。

```js
// 勝敗あり
GamePlazaSDK.sendResult(localPlayer.uuid, { score: 100 });

// 引き分け
GamePlazaSDK.sendResult(null);
```

- `winnerId`: `string | null` — 勝者の UUID。引き分けは `null`
- `scores`: `Record<string, number>` — 任意のスコア情報（省略可）

#### `GamePlazaSDK.sendClose()`

結果を送らずにゲームを閉じる（キャンセル・中断時）。

```js
GamePlazaSDK.sendClose();
```

### プロパティ（init 後に参照可能）

| プロパティ | 型 | 説明 |
|---|---|---|
| `GamePlazaSDK.players` | `Array` | 全プレイヤー |
| `GamePlazaSDK.localPlayer` | `Object \| null` | 操作中のプレイヤー |
| `GamePlazaSDK.gameType` | `string \| null` | ゲーム識別子 |

---

## meta.json 仕様

```json
{
  "title": "ゲームタイトル",
  "description": "ゲームの説明文（改行は \\n）"
}
```

プラットフォームがゲーム一覧で表示に使う。

---

## ローカル確認

`index.html` をブラウザで直接開くだけで動作確認できる（`file://` でOK）。

外部リソースを追加した場合など、静的サーバーが必要になったら：

```bash
npx serve .
```

プラットフォームからの `game_start` メッセージなしでも UI は表示される。

---

## デプロイ

GitHub Pages にデプロイし、プラットフォーム側の `game-config.ts` に URL を登録する。
