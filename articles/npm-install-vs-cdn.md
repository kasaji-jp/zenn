---
title: "CDNとnpm installってどう違うの？~stripeを例に~"
emoji: "🦓"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "nodejs", "stripe", "rabee"]
published: true
---

# 経緯
- プログラミングを勉強しはじめて1年ちょっと、逃げてきたこの違いについて勉強しようと思った。
- 初学者の方も、このあたり、なんとなくその場その場でググってでた方法でやってるのではないかと思った。
- 自分が実際にstripeのコードを書いてしっくりきたので記したいと思います。
- stripe：オンライン決済サービス。カード情報を入力すると、存在するカードかチェックして、トークンを発行。それをもとに決済ができる。


# 結論どう違うか
- CDNの方は、**windowオブジェクトにプラグインのオブジェクトが紐づくことになる**ので、ブラウザでだけ特定の機能を使う、という場合に有効です。
- 例えば、たまにうちの会社での開発ではアニメーション実装の為にピンポイントでjQueryを使う機会もあるんですが、ブラウザ上でしか使わないので、CDNで入れてます。
- 最近話題のTailwind.cssには、ドキュメントでcdn使えはするけど、[こんな制限](https://tailwindcss.com/docs/installation#using-tailwind-via-cdn)があるよ！と紹介してくれてます。
- そもそも使おうとしてるツールがどっちかしか用意していない場合もある。両方ある場合は、それぞれ想定されてる使い方が違うので、どの機能を使いたいのかに合わせて決める。
- ここからはstripeを例に、その違いをみていきましょう。

# stripeの場合
- tokenの取得だけなら、CDNでも全然できた。
- ただ、自分の場合はどうしても入力した値を一時的にフロントで取得する必要があったので、npm installを使いました。
- それぞれのやり方がどう違うかは下記の通りです。

## 1.CDNから読み込む方法
- stripeのドキュメントは[これ](https://stripe.com/docs/js)
- インストールはこれをindex.htmlとかに書くだけ
```js
  <script src="https://js.stripe.com/v3/"></script>
```
- そうすると、**windowオブジェクトにStripeというオブジェクトが紐づく**ので、こんな具合にセットアップする
- stripeの場合、まずDOMエレメントをmount(表示)させる必要があります。エレメントを使わずに、トークンを取得することはできません
```js
  <label for="card-element">Card</label>
  <div id="card-element"></div>

  <script>
    var stripe = Stripe('pk_test_TYooMQauvdEDq54NiTphI7jx');
    var elements = stripe.elements(); // cardのエレメントをつくる
    cardElement.mount('#card-element');
  </script>
```
- そんでもって、引数にcardElementを入れて、createTokenを呼ぶと、自動的にフォーム中から値を吸い上げて情報を送ってくれる
- ちなみに、idとかから値を取り出そうとするのはできない
```js
  stripe.createToken(cardElement).then(function(result) {
    // Handle result.error or result.token
  });
```

## 2.npm installする方法
- `npm install --save stripe`を打つと、**プロジェクトのnode_modulesにstripeのモジュールが追加されます。**
- node_modulesの中には、プロジェクトで追加されてるモジュール(ツールたち)が入っていて、どのバージョンを参照するかとかはpackage-lock.jsonとかに入ってきます。
- stripeのドキュメントは[これ](https://stripe.com/docs/api)
- 下のような感じで初期化
```js
  const Stripe = require('stripe');
  const stripe = Stripe('sk_test_4eC39HqLyjWDarjtT1zdp7dc');
```
- こんな感じでトークンを発行できます
```js
  const token = await stripe.tokens.create({
    card: {
      number: '4242424242424242',
      exp_month: 1,
      exp_year: 2022,
      cvc: '314',
    },
  });
```


以上、自分でも勉強になったので記事にしました！