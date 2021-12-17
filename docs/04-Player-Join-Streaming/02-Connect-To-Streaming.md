# 手順2: 配信に接続

ハンズオン最後の手順です。ここでは視聴画面からストリーミングに接続します。


## 2-1. Twilio Live Plpayer SDKをロード

*assets/player.html*ファイルをコードエディタで開き、56行目のコメントを解除します。この`twilio-live-player.min.js`が視聴アプリ用に提供されている[SDK](https://www.twilio.com/docs/live/javascript-player-sdk-overview)です。

```html
<!-- Twilio Live Player SDK -->
<script src='twilio-live-player.min.js'></script>
```

## 2-2. ボタンクリック時のロジックを実装

*assets/player.js*は視聴画面のロジックが実装されています。現在は下記のように`ストリーミングに参加`ボタンがクリックされた際のイベントハンドラには処理が実装されていません。

```js
    // 参加ボタンがクリックされた場合
    joinStreaming.addEventListener("click", async (event) => {
        event.preventDefault();
        // ここから実装を開始
        
    });
```

まずは現在接続しているホストおよびプロトコル情報を取得し、さらにブラウザーがストリーミングを表示できるかどうかを確認します。

```js
    // 参加ボタンがクリックされた場合
    joinStreaming.addEventListener("click", async (event) => {
        event.preventDefault();
        // ここから実装を開始
        // 現在接続しているホストとプロトコルを確認
        const {
            host,
            protocol
        } = window.location;
        
        // ブラウザーサポートを確認
        if (Player.isSupported) {
            // 2-3. サポートされている場合
    
        } else {
            console.log('サポートされていないブラウザです。');
        }
    });
```

## 2-3. アクセストークンをリクエストし、ストリーミングに接続

ブラウザーがサポートしている場合はアクセストークンをリクエストし、ストリーミングに接続します。

*// 2-3. サポートされている場合*コメント以降のコードを追加してください。

```js
    // 参加ボタンがクリックされた場合
    joinStreaming.addEventListener("click", async (event) => {
        event.preventDefault();
        // ここから実装を開始
        // 現在接続しているホストとプロトコルを確認
        const {
            host,
            protocol
        } = window.location;
        
        // ブラウザーサポートを確認
        if (Player.isSupported) {
            // 2-3. サポートされている場合
            // アクセストークンをリクエスト
            const response = await fetch('/live-access-token', {
                method: 'GET',
            });
    
            const {token} = await response.json();
    
            
            // アクセストークンを用いてストリーミングに接続
            const player = await Player.connect(token, {
                playerWasmAssetsPath: `${protocol}//${host}`,
            });

            if (player) {
                // ボタンの状態管理
                joinStreaming.disabled = true;
            }
            // 2-4.はこちらから開始

    
        } else {
            console.log('サポートされていないブラウザです。');
        }
    });
```

## 2-4. ストリーミングの再生を開始し、ビデオ要素を画面に追加

接続したストリーミングの再生を開始し、ビデオ要素を画面に追加します。

*// 2-4. サポートされている場合*コメント以降のコードを追加してください。

```js
    // 参加ボタンがクリックされた場合
    joinStreaming.addEventListener("click", async (event) => {
        event.preventDefault();
        // ここから実装を開始
        // 現在接続しているホストとプロトコルを確認
        const {
            host,
            protocol
        } = window.location;
        
        // ブラウザーサポートを確認
        if (Player.isSupported) {
            // 2-3. サポートされている場合
            // アクセストークンをリクエスト
            const response = await fetch('/live-access-token', {
                method: 'GET',
            });
    
            const {token} = await response.json();
    
            
            // アクセストークンを用いてストリーミングに接続
            const player = await Player.connect(token, {
                playerWasmAssetsPath: `${protocol}//${host}`,
            });

            if (player) {
                // ボタンの状態管理
                joinStreaming.disabled = true;
            }
            // 2-4.はこちらから開始
            // ストリーミングの再生を開始
            player.play();
    
            // 画面にビデオ要素を追加
            el = player.videoElement
            videoDiv.appendChild(el);

            // 2-5.はこちらから開始

    
        } else {
            console.log('サポートされていないブラウザです。');
        }
    });
```

## 2-5. ストリーミングの状態に合わせて処理を実装

ストリーミングの状態が変わると[`stateChanged`]()イベントが発生します。そのイベントハンドラを追加し、ストリーミングが終了した段階でビデオ要素を画面から削除します。

*// 2-5.はこちらから開始* コメント以降を実装してください。

```js
    // 参加ボタンがクリックされた場合
    joinStreaming.addEventListener("click", async (event) => {
        event.preventDefault();
        // ここから実装を開始
        // 現在接続しているホストとプロトコルを確認
        const {
            host,
            protocol
        } = window.location;
        
        // ブラウザーサポートを確認
        if (Player.isSupported) {
            // 2-3. サポートされている場合
            // アクセストークンをリクエスト
            const response = await fetch('/live-access-token', {
                method: 'GET',
            });
    
            const {token} = await response.json();
    
            
            // アクセストークンを用いてストリーミングに接続
            const player = await Player.connect(token, {
                playerWasmAssetsPath: `${protocol}//${host}`,
            });

            if (player) {
                // ボタンの状態管理
                joinStreaming.disabled = true;
            }
            // 2-4.はこちらから開始
            // ストリーミングの再生を開始
            player.play();
    
            // 画面にビデオ要素を追加
            el = player.videoElement
            videoDiv.appendChild(el);

            // 2-5.はこちらから開始
            // ストリーミングの状態が変更された場合
            player.on("stateChanged", (state) => {
                console.log(state);
                switch(state) {
                    case Player.State.Ended:
                        // ストリーミングが終了した場合はビデオ要素を削除し、ボタンの状態を変更する
                        videoDiv.removeChild(el);
                        el = undefined;
                        joinStreaming.disabled = false;
                        
                }
            });
        } else {
            console.log('サポートされていないブラウザです。');
        }
    });
```

## 2-6. テスト！

これで実装は完了です。配信画面、視聴画面をそれぞれ表示させ、視聴画面に正しく表示されることを確認してください。

最後に、念の為下記コマンドを実行し、不必要な課金が行われないように注意しましょう。


```bash
node delete-media-processors.js
```

全てを実装済みのサンプルアプリケーションはこちらのリポジトリから取得できます。

[github - @neri78/live-sample-jp/tree/completion](https://github.com/neri78/live-sample-jp/tree/completion)