# 手順1: PlayerStreamerとMediaProcessorを作成

この手順ではサーバー側のロジックを実装し、Twilio APIを呼び出し`PlayerStreamer`と`MediaProcessor`を作成します。

## 1-1. create-broadcast.jsを確認

`配信を開始`ボタンがクリックされると、*functions/create-broadcast.js*が呼び出されます。

現時点では以下のように環境変数の取得やレスポンスが実装されています。

```js
exports.handler = async function (context, event, callback) {

  // 環境変数からTwilio資格情報を取得
  const { ACCOUNT_SID, API_KEY, API_SECRET } = context;

  // 環境変数からSync Service SIDを取得
  const { SYNC_SERVICE_SID } = context;


  // ここから実装を開始


  callback(null, '');
};
```

## 1-2. リクエストからRoom SIDを取得し、Twilio Nodeヘルパーライブラリーを初期化

クライアントからは`JSON`形式で配信ルームの`Room SID`が送信されます。

Sync Service SIDを取得しているコードの下に次のコードを追加し、クライアントから渡される`Room SID`を取得します。

```js
  // Video Room SIDをリクエストから取得
  const roomSid = event.roomSid;
```

続いてTwilio APIをNode.jsから簡単に呼び出せるTwilio Nodeヘルパーライブラリーを初期化します。この際、Twilioの認証情報が必要となります。

```js
  // Twilio Nodeヘルパーライブラリを初期化
  const client = require('twilio')(
    API_KEY, 
    API_SECRET, 
    {accountSid: ACCOUNT_SID});
```

## 1-3. PlayerStreamerを作成

次に視聴アプリケーションに動画や音声データを配信する`PlayerStreamer`を作成します。次のコードを追加してください。

```js
  // PlayerStreamerを作成
  const playerStreamer = await client.media.playerStreamer.create();
  console.log(`PlayerStreamerが作成されました - ${playerStreamer.sid}`);
```

## 1-4. MediaProcessorを作成

配信ルームと先ほど作成したPlayerStreamerの中間でデータを処理し、動画や音声データを送るMediaProcessorを構築します。

この際、`extension` には動画や音声を処理するためにTwilioがホストする[`Video Composer`](https://jp.twilio.com/docs/live/video-composer)あるいは、[`Audio Mixer`](https://jp.twilio.com/docs/live/audio-mixer)の名前、またはURLを指定します。

さらに、`extensionContext`にはどのビデオルームからどのストリーミングに配信するかを指定します。

今回は下記のコードをそのまま利用します。

```js
  // MediaProcessorを作成
  const mediaProcessor = await client.media.mediaProcessor.create({
    extension: 'video-composer-v1',
    extensionContext: JSON.stringify({
      room: { name: roomSid },
      outputs: [playerStreamer.sid]
    })
  });
  console.log(`MediaProcessorが作成されました - ${mediaProcessor.sid}`);
```

アプリケーションを再起動し、[`http://localhost:3000/streamer.html`](http://localhost:3000/streamer.html)をブラウザーで開き、配信ルームに参加、そして`配信を開始`ボタンをクリックするとコンソールにそれぞれのインスタンスを作成したログが表示されます。

![Streamer App](../../assets/02-streaming-started.png)

---------------------------

コンソールに出力が確認できたらタブを閉じます。
ただし、`PlayerStreamer`および`MediaProcessor`は自動では停止せず、分ごとに課金されます。

上記のテストを行った後にプロジェクトのルートフォルダで下記コマンドを実行すると現在動作しているインスタンスを終了できます。

```bash
node delete-media-processors.js
```
---------------------------

## 次の手順

- [ストリーミングの情報を保存](02-Save-Data.md)