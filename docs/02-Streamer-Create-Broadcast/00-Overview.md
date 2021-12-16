#  2. 配信側アプリ - ストリーミング開始機能を実装

*assets/streamer.js* ファイルには以下のように`配信を開始`ボタンがクリックされた際のロジックが既に実装されています。

このロジックでは配信ルームの`Room SID`をサーバー側に`POST`リクエストで渡します。

```js
    // 配信開始ボタンのクリックイベント
    startStreamingBtn.addEventListener('click', async (event) => {
        event.preventDefault();

        // 配信開始APIを呼び出し
        let response = await fetch('/create-broadcast', {
            method: 'POST',
            headers: {
                "Content-Type" : "application/json"
            },
            body: JSON.stringify({roomSid: roomSid})
        });
        if (response && response.status === 200) {
            // ボタンの状態管理
            joinVideoRoomBtn.disabled = true;
            startStreamingBtn.disabled = true
            stopStreamingBtn.disabled = false;
        }
    });


```

このセクションではサーバー側のロジックを担う*functions/create-broadcast.js*内でTwilio Live APIを使用し、配信を開始するために必要な`PlayerStreamer`および`MediaProcessor`を作成します。

## ゴール
- Twilio Live APIを利用し`PlayerStreamer`、`MediaProcessor`を生成する方法を学習する
- Twilio Syncを用いて情報を保存する方法を学習する

## 手順
1. [PlayerStreamerとMediaProcessorを作成](01-Twilio-API-Live.md)
2. [ストリーミングの情報を保存](02-Save-Data.md)