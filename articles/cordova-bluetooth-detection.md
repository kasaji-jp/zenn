---
title: "cordovaでbluetoothイヤホンの接続状況を検知する"
emoji: "🎧"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "cordova", "ios", "android", "rabee"]
published: true
---


# 概要
- cordovaを使ったモバイルアプリ開発で、bluetoothイヤホンが接続されているかを確認するための実装です。
- [headset detection](https://github.com/juliusav/HeadsetDetection-PhoneGap-Plugin)というプラグインを使います。iosではこれでおk
- しかし、android版では、イヤホンの接続状態は検知できてるものの、端末本体のbluetooth on/offがキャッチできないことが判明
- 上記解決のため、[cordova-diagnostic-plugin](https://github.com/dpa99c/cordova-diagnostic-plugin#registerbluetoothstatechangehandler)を使って合わせ技一本。解決しました。

# 実装
- ほぼドキュメントの通りですが、こんな感じで実装しました。
- プラグインのインストールについては、各ドキュメントを参考にしてください。

```js
  // 初期値を取得 cordovaの状態取得は時間差があるので待つ
  let getHeadsetStatus = () => {
    return new Promise((resolve) => {
      window.HeadsetDetection.detect((detected) => {
        resolve(detected)
      });
    });
  };
  let isConnected = await getHeadsetStatus();

  //- イヤホンの着脱を監視
  window.HeadsetDetection.registerRemoteEvents((status) => {
    isConnected = status === "headsetAdded";
  });
  // bluetooth自体がoffにされたらheadsetの状態をoffにする
  cordova.plugins.diagnostic.registerBluetoothStateChangeHandler((state) => {
    if(state === cordova.plugins.diagnostic.bluetoothState.POWERED_OFF){
      isConnected = false;
    }
  });
```