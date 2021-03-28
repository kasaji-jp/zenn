---
title: "element.scrollIntoView() 任意の要素をスクロールさせる"
emoji: "📜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "frontend", "web", "rabee"]
published: true
---


# 概要
- element.scrollIntoView()は、jqueryも使わずに要素のスクロール位置をコントロールできる非常にありがたい機能です。https://developer.mozilla.org/ja/docs/Web/API/Element/scrollIntoView
- いつもは`({behavior: "smooth"})`をとりあえずoptionに突っ込んでいましたが、`block`や、`inline`プロパティの理解があまりできておらず、中でも`nearest`の存在を先日初めて知ったのでサンプルをつくって勉強してみました。
http://runstant.com/kasaji/projects/scrollintoview

# 引数

## option

### behavior
  - `auto`/`smooth`でアニメーションを指定。デフォは`auto`。`smooth`にするとヌルッと動く

### block
  - `start`/`end`/`nearest`で垂直方向の位置を指定。
  - start: 子要素がスクロールする親要素の上端で止まる
  - end: 子要素がスクロールする親要素の下端で止まる
  - start: 子要素が今いるスクロール位置から、`start`/`end`の近い方の位置で止まる

### inline
  - `start`/`end`/`nearest`で水平方向の位置を指定。
  - start: 子要素がスクロールする親要素の左端で止まる
  - end: 子要素がスクロールする親要素の右端で止まる
  - start: 子要素が今いるスクロール位置から、`start`/`end`の近い方の位置で止まる

## bool値
- `element.scrollIntoView()`で`element.scrollIntoView({block: "start", inline: "nearest"})`と同じ効果
- `element.scrollIntoView(false)`で`element.scrollIntoView({block: "end", inline: "nearest"})`と同じ効果

# サンプルについて
![image.png](https://firebasestorage.googleapis.com/v0/b/alog-rabee-jp.appspot.com/o/users%2FSfwgYE0OUCV5HJaRSfh0YVRV0Nc2%2Ffiles%2F1616971578.png?alt=media&token=fce82e0b-0328-41a7-973a-58d64f17b7a6)
- 左ではblockを、右ではinlineの見た目を試せます。(方向が変わっただけですが、、、)
- scrollボタンを押すと、今は画面外にいる水色の子要素がヌルッとフレームインしてきます。
- セレクトボックスで各プロパティのvalueを変更できます。`nearest`はどっち側にフレームアウトしているかによって挙動が変わるのがわかると思います。
- `{behavior: 'smooth'}`は共通で入れています。
http://runstant.com/kasaji/projects/scrollintoview