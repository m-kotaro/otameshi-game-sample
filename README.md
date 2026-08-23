# otameshi-game-sample

Game Plaza 用の外部ゲームテンプレートです。  
このリポジトリをフォークして、自分のゲームを実装してください。

## 構成

```
index.html   ... ゲーム本体（HTML + JS 単一ファイル）
meta.json    ... ゲームのメタ情報（タイトル・説明文）
```

## 使い方

1. このリポジトリをフォーク（またはコピー）
2. `index.html` の「ゲームロジック」セクションを書き換える
3. `meta.json` のタイトル・説明文を自分のゲームに合わせて更新
4. GitHub Pages 等にデプロイ

## プラットフォーム連携

`GamePlazaSDK` が組み込み済みです。以下の API を使ってプラットフォームと通信します。

### 受信

```js
GamePlazaSDK.init(({ gameType, players, localPlayer }) => {
  // ゲーム開始時に呼ばれる
  // players: 全プレイヤー配列
  // localPlayer: 操作しているプレイヤー
});
```

### 送信

```js
// ゲーム結果を送信（winnerId: 勝者UUID, null で引き分け）
GamePlazaSDK.sendResult(winnerId, { score: 100 });

// ゲームを閉じる（結果なしで終了）
GamePlazaSDK.sendClose();
```

## ローカル確認

```bash
# 任意の静的サーバーで開く
npx serve .
# または
python3 -m http.server 8080
```

ブラウザで開けば動作確認できます。  
プラットフォームからの `game_start` メッセージなしでも UI は表示されます。
