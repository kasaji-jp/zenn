---
title: "Firestore データ取得の制限について"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["firebase", "javascript", "rabee"]
published: true
---

# 概要
- 普段大変お世話になっているFirestoreですが、DBから直接データを取得しようとすると、制限があるので自分なりにまとめました。
- 今回は下記の様な例で説明していきたいと思います。4人の男の子の身長と体重が入ったDBです。

**collection名：boys**
|id|name|height|weight
|--|--|--|--
|0|一郎|160|60
|1|二郎|165|65
|2|三郎|170|70
|3|四郎|175|75
|3|五郎|180|80
|3|六郎|185|85



# orderByの制限
## 不等号を使った複数フィールドの絞り込みができない
- 例のデータだと、例えば、身長160cm~170cmかつ、体重65kg~80kgの人、みたいな絞り込みが仕様上できません。
```js
  let boysRef = db.collection("boys").where("height", ">=", 160).where("height", "<=", 170).where("weight", ">=", 65).where("weight", "<=", 80); 
  let items = await boysRef.get(); // エラー
```
- 出てくるエラーはこんな感じ。`不等号を含むwhereフィルターは全て同じフィールドに対しての指定である必要があります`みたいに言われます。
- 今回の例でいうと、身長160cm~170cmの人、体重65kg~80kgの人、どっちか片方だけの絞り込みはできます。
```
Uncaught (in promise) FirebaseError: Invalid query. All where filters with an inequality (<, <=, !=, not-in, >, or >=) must be on the same field. But you have inequality filters on 'height' and 'weight'
```

## 不等号をつかった絞り込みと、orderByに異なるフィールドを指定できない
- 今回の例だと、例えば、身長160cm~170cmの人に絞り込みつつ、体重を降順に並び替える、というのができません。
```js
  let boysRef = db.collection("boys").where("height", ">=", 160).where("height", "<=", 170).orderBy("weight", "desc");
  let items = await boysRef.get(); // エラー
```
- こんなエラーです。`不等号を含むwhereフィルターはheightについてのものなので、最初のorderByのフィルターも、heightについて指定する必要があります`ということでした。
```
Uncaught (in promise) FirebaseError: Invalid query. You have a where filter with an inequality (<, <=, !=, not-in, >, or >=) on field 'height' and so you must also use 'height' as your first argument to Query.orderBy(), but your first orderBy() is on field 'weight' instead.
```
- ちなみに、2個目のorderByにweightを指定する↓のやり方だとエラーはでませんが、身長順で並び替えたあと、同順位のデータを体重順で並び替えるだけなので、期待する結果は得られません。
```js
  let boysRef = db.collection("boys").where("height", ">=", 160).where("height", "<=", 170).orderBy("height", "desc").orderBy("weight", "desc");
  let items = await boysRef.get();
```


# ページネーションが実現しづらい
- まずfirestoreを使ってのページングにはstartAfterを使います。上の例だと以下の通りです。
```js
  let boysRef = db.collection("boys").orderBy("height", "asc").limit(2);
  let items = await boysRef.get(); // itemsには一郎、二郎のデータ
  let last = items[items.length - 1];
  let nextItems = boysRef.startAfter(last.doc).get(); // 三郎、四郎のデータ
```
- 上記のやり方だと`もっと見る`形式の追加ローディングにはさくっと対応できますが、googleの検索結果ページにある様なページネーションには対応が困難です。
- 例えば、100件データがあるうちの51件目から表示したい場合、一発で51件目のデータを特定することが、startAfterでは不可能です。

# 参考リンク
https://firebase.google.com/docs/firestore/query-data/order-limit-data?hl=ja