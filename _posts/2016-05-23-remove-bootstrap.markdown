---
layout: post
title: "Railsプログラマが '脱 Bootstrap依存' をするために工夫したこと & デザイナーから指摘を受けたTips"
date: 2016-05-23 9:41:28 +0900
categories: rails
image: "https://blog.regonn.tokyo/images/2016-05-23/4.png"
---

WEB プログラマがサイトデザインを整えるにとても便利な[Bootstrap](http://getbootstrap.com/)。
しかし、Bootstrap を利用してサイトを作ると、やっぱり Bootstrap 臭がしてしまう。(私の技術力やデザイン力が低いのが原因かもしれませんが。。。)

そこで、Bootstrap を利用してデザインをしている Rails のサイトをデザイナーの人にレビューしてもらい、実践した **Bootstrap の依存を減らしていくために工夫したこと & デザインの指摘を受けた部分** について書いていきます。

# 実装したサイトの説明

まず作っていたサイトについて説明しておきますと、 _企業がイベントを開催した際にフィードバックを受けるためのアンケートを WEB 上で回答してもらい、その集計結果が見えるというサービス_ です。機能としては少なく、企業管理側のイベントページの作成と詳細ページを見ることができるだけです。

# Before and After

まず、サイトがどう変わったかの画像を載せておきます。(サイトが特定される情報の部分は隠してあります。)

## Before

まずは、デザイン修正前の画像です。どのページもすごい Bootstrap 臭がプンプンしてますね。

### ログイン画面

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/1.png" alt="Bootstrapログイン" width="2840" height="1508" layout="responsive" ></amp-img>

### ログイン後の画面

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/2.png" alt="Bootstrapダッシュボード" width="2840" height="1508" layout="responsive" ></amp-img>

### イベント詳細ページ

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/3.png" alt="Bootstrapイベント詳細" width="2830" height="1520" layout="responsive" ></amp-img>

### イベント作成ページ

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/4.png" alt="Bootstrapイベント作成" width="2866" height="1486" layout="responsive" ></amp-img>

## After

続いて、デザイナーから指摘を受けて修正したものです、全体的に洗練されてますね。（デザイナーの方にコードを触ってもらったわけではなく、指摘部分を私が直しているので、まだまだ改善の余地はありそうですが）

### ログイン画面

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/5.png" alt="ログイン" width="1218" height="785" layout="responsive" ></amp-img>

### ログイン後の画面

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/6.png" alt="ダッシュボード" width="1243" height="823" layout="responsive" ></amp-img>

### イベント詳細ページ

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/7.png" alt="イベント詳細" width="1244" height="690" layout="responsive" ></amp-img>

### イベント作成ページ

<amp-img src="https://blog.regonn.tokyo/images/2016-05-23/8.png" alt="イベント作成" width="1239" height="741" layout="responsive" ></amp-img>

# 最初に注意を受けた部分

デザイナーの人に見せてまず言われたことが次の 2 つです

- デザインのコンセプトを決める
- 基本 Bootstrap の機能は Grid システムだけにしてみる

それぞれについて書いていきます。

## デザインのコンセプトを決める

デザインのコンセプトを決めないでいるため、 **Bootstrap の色がそのまま使われてしまっているなど、オリジナル感が出しにくくなってしまっていました** 。最初に、どのようなデザインにするのかコンセプトを決めることが必要のようです。

そこで、依頼者からの話などをもとに次のようなコンセプトに決めました。

- 基調を白として、アクセントに赤を入れる
- パディングなども大きさをもたせてゆとりを作り高級感を出す

## 基本 Bootstrap の機能は Grid システムだけにしてみる

Bootstrap のボタンをそのまま使うと色が決まっていたり(カスタムはできますが)、丸みを帯びたボタンになってしまったりするため、基本的に Bootstrap は縦を揃えるための Grid 以外は使わずに、他は自分で実装していった方が結局はデザインしやすいとのこと。Bootstrap 自体を外すことも考えましたが、Bootstrap は v4 になると flexbox([flexbox の分かりやすいサンプル - Bootstrap 4 Flex Box Grid Demo](http://codepen.io/ncerminara/pen/EjqbPj))等の便利な機能が追加されるのと、改修する部分が多くなりそうなので v4 の一部分を import して使いました。

# Bootstrap の依存を減らすために利用したライブラリ

- [bootstrap-ruby-gem](https://github.com/twbs/bootstrap-rubygem)
  rails で bootstrap を利用する場合には [bootstrap-sass](https://github.com/twbs/bootstrap-sass) gem が有名ですが、bootstrap-ruby-gem は bootstrap version 4 に対応している gem です。bootstrap の form や modal はそのまま利用したかったので application.sass では次のように import して使っています。

```sass
@import bootstrap-grid
@import "bootstrap/mixins"
@import "bootstrap/forms"
@import "bootstrap/modal"
```

- [auto-prefixer-rails](https://github.com/ai/autoprefixer-rails)
  ベンダープレフィックスを簡単に生成してくれる gem です。[autoprefixer を使って快適にコーディングする](http://paranishian.hateblo.jp/entry/2015/12/29/142219)
- [sassc-rails](https://github.com/sass/sassc-rails)
  sass の C 実装です。ローカル開発中でリロードした際に体感でわかるぐらい早くなります。heroku 上でも問題なく利用できました。実際の sass ruby 実装と生成されるものの違いが気になる方は [sassmeister](http://www.sassmeister.com/)というサイトで options から libsass を選ぶと sass C 実装で生成されるコードが確認できます。
- [bourbon](https://github.com/thoughtbot/bourbon)
  sass の mixin 集です。ボタンがホバーした時に少し明るくしたり、暗くしたりするために、tint や shade が便利です、lighten や darken よりも自然に色を明るしてくれます。[CSS フレームワーク Bourbon 超速入門](http://qiita.com/nekoneko-wanwan/items/99d4650768c46ae41897#functions)

# 大きさの単位を px ではなく rem を使う

[CSS の基本単位として rem を使うと超絶便利 - Qiita](http://qiita.com/butchi_y/items/453654828d9d6c9f94b0) でもあるようにレスポンシブなサイトを作るときに px と rem を使い分けると作りやすいみたいです。
ただ、rem は `360px = 22.5rem` など、計算が必要になってくるので sass の function を作ってしまいます。

```sass
$base-px: 16px

@function strip_unit($number)
  @if number($number)
    @return $number / ($number * 0 + 1)

@function px($value)
  @if number($value) and unit($value) == "px"
    @return true
  @else
    @return null

@function px_to_rem($px, $base-px: $base-px)
  @if strip_unit($px) == 0
    @return 0
  @else if px($px)
    @return (strip_unit($px) / strip_unit($base-px)) * 1rem
  @else
    @return $px
```

これで

```sass
max-width: px_to_rem(826px) // 51.625rem
```

のように rem を利用したサイズ指定が利用しやすくなります。

# 細かい Tips

## 全体的に色を揃える

色を決めると、リンクの色が青になっていて目立ったりしてしまうので、テーマの色(今回は黒)にして下線を常に出してリンクとわかるようにしました。

## Panel を使わない

何かと情報をまとめる時に[Panel](http://getbootstrap.com/components/#panels)を使っていたんですが、Bootstrap v4 からは消えてしまう機能なのでなるべく使わずに。今回は h2 を利用してどこまでが情報のまとまりかをわかるように表示。

## table には交互に色をつける

これは Bootstrap の機能をはずしたので

```sass
tr:nth-child(even)
  background-color: #F2F2F2
```

のように対応。

## 単位は小さく

15 人や 10%の "人" や "%" の **単位部分を小さくすることで、数字(データ)が見やすくなる**。

## DT(項目名)は目立たなくさせる

"開催日時" などの **項目名を表す部分は、必要ではあるけど実際の情報に注目して欲しいのであくまで補助という形で色を薄く** しました。

## 左寄せと、右寄せは意識して使い分ける

例えば、金額などを表示するときには、桁数を揃えて表示したいので右寄せを利用する。
中央寄せを利用していて、タイトルなどの目立たせたいところは他と差異を作るために、そこだけ左寄せを利用する。

## 想定するフォームの幅にする

これは、まだ未実装ですが Bootstrap のフォームを使っていると `width: 100%` のスタイルが適用されるので、全体的に間延びした印象を与えてしまうので、ちゃんと想定する入力の長さを決めて幅を整える。

## 削除のリンクなど取り扱いに注意が必要なものは色を目立つように

画面一覧の画像にはないですが、削除するためのリンクなどは、利用者に注意を促す意味もあり、普通のリンクと色を変える。

# 最後に

今回の紹介は以上です。他のサイトでも Bootstrap を外してみたり、今回実装したサイトでも何かさらに進展があったら加筆していきます。
デザインをデザイナーの方に頼らず、ある程度構築できるようになりたいですね。
