#  3. 配信側アプリ - ストリーミング停止機能を実装

*assets/streamer.js* ファイルには以下のように`配信を停止`ボタンがクリックされた際のロジックが既に実装されています。


```js
    // 配信停止ボタンのクリックイベント
    stopStreamingBtn.addEventListener('click', async (event) => {
        event.preventDefault();
        
        // 配信停止APIを呼び出し
        let response = await fetch('/stop-broadcast', {
            method: 'POST',
        });
        if (response && response.status === 200) {
            // ボタンの状態管理
            joinVideoRoomBtn.disabled = true;
            startStreamingBtn.disabled = false
            stopStreamingBtn.disabled = true;
        
        }
    });
```

このセクションではサーバー側のロジックを担う*functions/stop-broadcast.js*内でTwilio Live APIを使用し、配信を停止するロジックを実装します。

## ゴール
- 配信を停止するための処理を実装する

## 手順
1. [Twilio Syncから配信情報を取得しインスタンスを停止](01-load-data-from-sync.md)
