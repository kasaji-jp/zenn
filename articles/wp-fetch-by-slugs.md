---
title: "WordPress REST API タグのslug名でデータを取得する"
emoji: "🏷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["wordpress", "javascript", "rabee"]
published: true
---


# 概要
- REST APIをつかったpostの取得の話です。
- wordpress4.7以降は、セキュリティ上の都合で、それまでできていたタグのslug名での直接の絞り込みができなくなったそうな。[参考リンク](https://wordpress.org/support/topic/call-wordpress-rest-api-using-multiple-tags/)
- いろいろ調べてうまくいった方法があった、かつ(多分)日本語でズバリの情報がなかったので、記録しておきます。もっといいやり方があれば、教えてください！


# 実装
1. まずは、タグのidを突き止めます
1. 取得したidを使ってfetch!以上！


```js
  // 指定したslugでタグ(今回は"hoge")のid取得 
  let res = await fetch('https://sample.com/wp-json/wp/v2/tags?slug=hoge');
  let tags = await res.json();

  // 上記idをもとにアイテムを取得
  let tag = tags[0];
  if (tag) {
    let res = await fetch(`https://sample.com/wp-json/wp/v2/news?tags=${tag.id}`);
    let posts = await res.json();
  }
```