---
title: "画像や動画のオリジナルサイズを取得する方法"
emoji: "🎥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "javascript", "rabee"]
published: true
---


# 情報が入っているプロパティ
- 画像の場合、naturalWidth/natralHeight
- 動画の場合、videoWidth/videoHeight
- 実際に取得する方法については後述します。


# ソールの情報が取れるタイミングを待つ
- タグのsrcが読み込まれてからじゃないと、それぞれ情報が取れてきません。準備ができたタイミングで取得しましょう。
- imgタグの場合、[onloadというイベント](https://www.w3schools.com/tags/ev_onload.asp)で取得。
- videoタグの場合、[イベント](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video)はいくつかバリエーションがあるんですが、progressというイベントのタイミングで情報が取れます。
- 画像や動画を画面に表示させる必要がない場合は、jsで表示させない要素を作ります
- その上で、各タイミングで取れた値をjsで取得するのがいいでしょう
- それぞれのバリエーションでの例を示します

## 表示させているimgタグから値を取得
```html
  <img src="..." onload="getImageWidth()"/>
```
```js
  const getImageWidth = () => {
    let width = $("img").naturalWidth;
    console.log(width);
  };
```

## 動画は表示させず、widthを取得する場合
- 下記の関数に動画のurlを渡せば値を取得できます。
```js
  // videoのurlを引数に入れる関数
  fetchVideoInfo(url) {
    return new Promise((resolve) => {
      //- ビデオタグを生成してサイズ取得
      var video = document.createElement('video');
      // srcにセット
      video.src = url;
      // progressイベントが呼ばれたときにでresolve関数を発火する
      video.addEventListener('progress', () => {
        resolve ({
          width: video.videoWidth,
          height: video.videoHeight,
        });
      });
    });
  }
```
