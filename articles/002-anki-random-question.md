---
title: "ankiで選択肢をランダム表示にする"
emoji: "📚"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["anki", "学習", "勉強"]
published: true
---

## はじめに

IT の資格の勉強にいつも anki というフラッシュカードアプリを使っています。
anki は素晴らしいアプリで、色々な方が素晴らしさを語っているのでここでは使い方などは割愛しますが、PC で暗記カードの管理がしやすいので愛用しています。

anki を使った学習効率の高めかたは、ふろむださんの本が一番参考になります

https://booth.pm/ja/items/2423461

anki の基本は、表となる面に問題を書いて、裏となる面にその解答をのせる感じなのですが、基本的な使い方は一問一答形式です。選択肢式は記憶の定着として優れていないので、公式でもあまり推奨されていない学習法のようです。

ですが今やってる資格の過去問が選択式なので、anki の出題も選択肢を出すようにしています。例えば ↓ のような感じです

![anki表カード](/images/002/002-anki-random-question-001.png)

フラッシュカードなので、機能として選択肢から選ぶということもできないですし、毎回同じ順番で出題されるので数周すると場所で答えを覚えてしまうのが欠点です。

そこで簡易に選択肢をランダムにさせる方法をここに記載します

## anki の選択肢ランダム化

anki は後ろで jQuery が動いているので、それを使って簡易的に作リます。多少このやり方の問題点はあるのですが、そこまでこれで不便を感じてないので大丈夫かと思います

### anki カードの設定

表面に表示させる問題部分ですが、選択肢の部分をリストにします

![anki表カード](/images/002/002-anki-random-question-002.png)

### カードテンプレートの設定

カードのテンプレート編集画面に行き、`表面のテンプレート`に script を埋め込みます

```javascript
<script>
(() => {
  var container = $('.question ol');
	var content = container.find("li");
	var total = content.length;
  content.each(function() {
    content.eq(Math.floor(Math.random()*total)).prependTo(container);
	});
})()
</script>

/* 中間は割愛 */

/* 問題部分を識別できるように、以下のように<div class="question">で囲います */
<div class="question">
{{Question}}
</div>
```

`var container = $('.question ol');`の部分が重要で、`question`クラス配下のリストをランダムにするようになっているので、私の場合は`{Question}`ですが、この部分を必ず`div class` で囲ってあげて下さい。

### 最終的なテンプレート

最終的に私のテンプレートはこんな感じになっています。

#### 表面テンプレート

![表テンプレート](/images/002/002-anki-random-question-003.png)

#### 裏面テンプレート

![裏テンプレート](/images/002/002-anki-random-question-004.png)

#### 書式テンプレート

![書式テンプレート](/images/002/002-anki-random-question-005.png)

## 問題点

解答の表示を押すと、画面がリフレッシュするので再度ランダム処理が走ります。なので、解答を見た時は順番が変わってしまっているので、選択肢の番号を覚えるのではなく、ちゃんと解答した中身を覚えておく必要があります(結果としてちゃんと覚えるのでいいかもですが)。