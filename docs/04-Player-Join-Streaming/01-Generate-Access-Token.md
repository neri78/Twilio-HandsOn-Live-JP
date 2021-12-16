# 手順1: アクセストークンを作成

この手順では視聴画面からストリーミングにアクセスするためのトークンをサーバー側で作成します。

## 1-1. live-access-token.jsを確認

アクセストークンの作成は*functions/live-access-token.js*で実装します。

現時点では以下のように環境変数の取得やレスポンス、Twilio Nodeヘルパーライブラリを使用してSync Document Resourceを取得しています。

```js
exports.handler = async function (context, event, callback) {

  // Twilio資格情報を取得
  const { ACCOUNT_SID, API_KEY, API_SECRET } = context;

  // 環境変数からSync Service SIDを取得
  const { SYNC_SERVICE_SID } = context;

  // CallBack関数で渡すエラー、およびレスポンスオブジェクト
  let error = null;
  let response = null;

  // Twilio Nodeヘルパーライブラリを初期化
  const client = require('twilio')(
    API_KEY, 
    API_SECRET, 
    {accountSid: ACCOUNT_SID});

  // Syncに登録するドキュメントの名前を指定
  const documentName = 'streaming_info';

  // SyncオブジェクトからPlayerStreamerSIDを取得
  let syncDocument = await client.sync.services(SYNC_SERVICE_SID)
    .documents(documentName)
    .fetch();
  
  // ここから実装を開始


  // ここより下は変更しない

  // コールバック関数を呼び出し
  callback(error, response)
};
```

## 1-2. PlayerStreamer SIDを取得

下記のコードを追加し、Sync Document ResourceからPlayerStreamer SIDを取得します。取得できない場合はエラーとして取り扱います。

```js
// ... 省略

  // Documentを取得できている場合
  if (syncDocument) {
    
    // SyncオブジェクトからPlayerStreamerSIDを取得
    let playerStreamerSid = syncDocument.data.playerStreamerSid;

    
    // 1-3. はここから実装予定

  } else {
    // このブロックに到達した場合はエラーとなる
    error = '問題が発生しました。'
  }

// ... 省略
```

## 1-3. トークンを作成

取得したPlayerStreamer SIDのストリーミングを再生できる権限を持ったトークンを作成します。

*// 1-3. はここから実装予定* コメント以降のコードを追加してください。



```js
// ... 省略

  // Documentを取得できている場合
  if (syncDocument) {
    
    // SyncオブジェクトからPlayerStreamerSIDを取得
    let playerStreamerSid = syncDocument.data.playerStreamerSid;
    
    // 1-3. はここから実装予定
    // アクセストークン並びにストリーミング再生権限を付与
    const { AccessToken } = Twilio.jwt;
    const { PlaybackGrant } = AccessToken;

    // アクセストークンの作成
    const accessToken = new AccessToken(
      ACCOUNT_SID, 
      API_KEY,
      API_SECRET);

    // 指定したPlayerStreamer IDに関する再生権限を取得
    let playbackGrant = await client.media.playerStreamer(playerStreamerSid)
      .playbackGrant()
      .create({ttl: 60});
    
    const wrappedPlaybackGrant = new PlaybackGrant({
      grant: playbackGrant.grant
    });

    // アクセストークンに権限を付与
    accessToken.addGrant(wrappedPlaybackGrant);

    // 1-4.はこちらから実装予定


  } else {
    // このブロックに到達した場合はエラーとなる
    error = '問題が発生しました。'
  }

// ... 省略
```

## 1-4. レスポンスにトークンを設定

最後にレスポンスのBodyとしてトークンを設定します。

*// 1-4. はここから実装予定* コメント以降のコードを追加してください。

```js
// ... 省略

  // Documentを取得できている場合
  if (syncDocument) {
    
    // SyncオブジェクトからPlayerStreamerSIDを取得
    let playerStreamerSid = syncDocument.data.playerStreamerSid;
    
    // 1-3. はここから実装予定
    // アクセストークン並びにストリーミング再生権限を付与
    const { AccessToken } = Twilio.jwt;
    const { PlaybackGrant } = AccessToken;

    // アクセストークンの作成
    const accessToken = new AccessToken(
      ACCOUNT_SID, 
      API_KEY,
      API_SECRET);

    // 指定したPlayerStreamer IDに関する再生権限を取得
    let playbackGrant = await client.media.playerStreamer(playerStreamerSid)
      .playbackGrant()
      .create({ttl: 60});
    
    const wrappedPlaybackGrant = new PlaybackGrant({
      grant: playbackGrant.grant
    });

    // アクセストークンに権限を付与
    accessToken.addGrant(wrappedPlaybackGrant);

    // 1-4.はこちらから実装予定
    // レスポンスを作成
    response = new Twilio.Response();

    response.appendHeader('Access-Control-Allow-Origin', '*');
    response.appendHeader('Access-Control-Allow-Methods', 'GET');
    response.appendHeader('Access-Control-Allow-Headers', 'Content-Type');
    response.appendHeader('Content-Type', 'application/json');
    // レスポンスにトークンを追加
    response.setBody({ token: accessToken.toJwt() });

  } else {
    // このブロックに到達した場合はエラーとなる
    error = '問題が発生しました。'
  }

// ... 省略
```

こちらがすべてのコードです。迷った場合は参考にしてください。

```js
exports.handler = async function (context, event, callback) {

  // Twilio資格情報を取得
  const { ACCOUNT_SID, API_KEY, API_SECRET } = context;

  // 環境変数からSync Service SIDを取得
  const { SYNC_SERVICE_SID } = context;

  // CallBack関数で渡すエラー、およびレスポンスオブジェクト
  let error = null;
  let response = null;

  // Twilio Nodeヘルパーライブラリを初期化
  const client = require('twilio')(
    API_KEY, 
    API_SECRET, 
    {accountSid: ACCOUNT_SID});

  // Syncに登録するドキュメントの名前を指定
  const documentName = 'streaming_info';

  // SyncオブジェクトからPlayerStreamerSIDを取得
  let syncDocument = await client.sync.services(SYNC_SERVICE_SID)
    .documents(documentName)
    .fetch();
  
  // ここから実装を開始
  // Documentを取得できている場合
  if (syncDocument) {
    
    // SyncオブジェクトからPlayerStreamerSIDを取得
    let playerStreamerSid = syncDocument.data.playerStreamerSid;

    
    // 1-3. はここから実装予定
    // アクセストークン並びにストリーミング再生権限を付与
    const { AccessToken } = Twilio.jwt;
    const { PlaybackGrant } = AccessToken;

    // アクセストークンの作成
    const accessToken = new AccessToken(
        ACCOUNT_SID, 
        API_KEY,
        API_SECRET);

    // 指定したPlayerStreamer IDに関する再生権限を取得
    let playbackGrant = await client.media.playerStreamer(playerStreamerSid)
        .playbackGrant()
        .create({ttl: 60});
    
    const wrappedPlaybackGrant = new PlaybackGrant({
        grant: playbackGrant.grant
    });

    // アクセストークンに権限を付与
    accessToken.addGrant(wrappedPlaybackGrant);

    // 1-4.はこちらから実装予定
    // レスポンスを作成
    response = new Twilio.Response();

    response.appendHeader('Access-Control-Allow-Origin', '*');
    response.appendHeader('Access-Control-Allow-Methods', 'GET');
    response.appendHeader('Access-Control-Allow-Headers', 'Content-Type');
    response.appendHeader('Content-Type', 'application/json');
    // レスポンスにトークンを追加
    response.setBody({ token: accessToken.toJwt() });

  } else {
    // このブロックに到達した場合はエラーとなる
    error = '問題が発生しました。'
  }

  // ここより下は変更しない

  // コールバック関数を呼び出し
  callback(error, response)
};
```

これで接続に必要なトークンを生成できました。次の手順では視聴側画面からストリーミングに接続します。

## 次のセクション/手順

- [配信に接続](02-Connect-To-Streaming.md)