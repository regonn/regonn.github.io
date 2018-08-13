---
layout: post
title: "Railsプログラマが '脱 Bootstrap依存' をするために工夫したこと & デザイナーから指摘を受けたTips"
date: 2016-05-23 9:41:28 +0900
categories: rails
---

WEBプログラマがサイトデザインを整えるにとても便利な[Bootstrap](http://getbootstrap.com/)。
しかし、Bootstrapを利用してサイトを作ると、やっぱりBootstrap臭がしてしまう。(私の技術力やデザイン力が低いのが原因かもしれませんが。。。)

そこで、Bootstrapを利用してデザインをしているRailsのサイトをデザイナーの人にレビューしてもらい、実践した **Bootstrapの依存を減らしていくために工夫したこと & デザインの指摘を受けた部分** について書いていきます。

# 実装したサイトの説明
まず作っていたサイトについて説明しておきますと、 *企業がイベントを開催した際にフィードバックを受けるためのアンケートをWEB上で回答してもらい、その集計結果が見えるというサービス* です。機能としては少なく、企業管理側のイベントページの作成と詳細ページを見ることができるだけです。

# Before and After
まず、サイトがどう変わったかの画像を載せておきます。(サイトが特定される情報の部分は隠してあります。)
## Before
まずは、デザイン修正前の画像です。どのページもすごいBootstrap臭がプンプンしてますね。
### ログイン画面
![regonn1.png](/images/2016-05-23/1.png)
### ログイン後の画面
![regonn2.png](/images/2016-05-23/2.png)
### イベント詳細ページ
![regonn3.png](/images/2016-05-23/3.png)
### イベント作成ページ
![regonn4.png](/images/2016-05-23/4.png)
## After
続いて、デザイナーから指摘を受けて修正したものです、全体的に洗練されてますね。（デザイナーの方にコードを触ってもらったわけではなく、指摘部分を私が直しているので、まだまだ改善の余地はありそうですが）
### ログイン画面
![regonn5.png](/images/2016-05-23/4.png)


### ログイン後の画面
![regonn6.png](/images/2016-05-23/5.png)


### イベント詳細ページ
![regonn7.png](/images/2016-05-23/6.png)


### イベント作成ページ
![regonn8.png](/images/2016-05-23/7.png)

# 最初に注意を受けた部分
デザイナーの人に見せてまず言われたことが次の2つです

* デザインのコンセプトを決める
* 基本 Bootstrap の機能はGridシステムだけにしてみる

それぞれについて書いていきます。

## デザインのコンセプトを決める
デザインのコンセプトを決めないでいるため、 **Bootstrapの色がそのまま使われてしまっているなど、オリジナル感が出しにくくなってしまっていました** 。最初に、どのようなデザインにするのかコンセプトを決めることが必要のようです。

そこで、依頼者からの話などをもとに次のようなコンセプトに決めました。

+ 基調を白として、アクセントに赤を入れる
+ パディングなども大きさをもたせてゆとりを作り高級感を出す

## 基本 Bootstrap の機能はGridシステムだけにしてみる
Bootstrap のボタンをそのまま使うと色が決まっていたり(カスタムはできますが)、丸みを帯びたボタンになってしまったりするため、基本的に Bootstrap は縦を揃えるためのGrid以外は使わずに、他は自分で実装していった方が結局はデザインしやすいとのこと。Bootstrap 自体を外すことも考えましたが、Bootstrapはv4になるとflexbox([flexboxの分かりやすいサンプル - Bootstrap 4 Flex Box Grid Demo](http://codepen.io/ncerminara/pen/EjqbPj))等の便利な機能が追加されるのと、改修する部分が多くなりそうなのでv4の一部分をimportして使いました。

# Bootstrapの依存を減らすために利用したライブラリ

* [bootstrap-ruby-gem](https://github.com/twbs/bootstrap-rubygem)
	railsでbootstrapを利用する場合には [bootstrap-sass](https://github.com/twbs/bootstrap-sass) gem が有名ですが、bootstrap-ruby-gem は bootstrap version 4 に対応しているgemです。bootstrapのformやmodalはそのまま利用したかったので application.sass では次のようにimportして使っています。

``` sass
@import bootstrap-grid
@import "bootstrap/mixins"
@import "bootstrap/forms"
@import "bootstrap/modal"
```

* [auto-prefixer-rails](https://github.com/ai/autoprefixer-rails)
	ベンダープレフィックスを簡単に生成してくれるgemです。[autoprefixerを使って快適にコーディングする](http://paranishian.hateblo.jp/entry/2015/12/29/142219)
* [sassc-rails](https://github.com/sass/sassc-rails)
	sassのC実装です。ローカル開発中でリロードした際に体感でわかるぐらい早くなります。heroku上でも問題なく利用できました。実際のsass ruby実装と生成されるものの違いが気になる方は [sassmeister](http://www.sassmeister.com/)というサイトで optionsからlibsassを選ぶとsass C実装で生成されるコードが確認できます。
* [bourbon](https://github.com/thoughtbot/bourbon)
	sassのmixin集です。ボタンがホバーした時に少し明るくしたり、暗くしたりするために、tintやshade が便利です、lightenやdarkenよりも自然に色を明るしてくれます。[CSSフレームワークBourbon超速入門](http://qiita.com/nekoneko-wanwan/items/99d4650768c46ae41897#functions)

# 大きさの単位を px ではなく rem を使う
[CSSの基本単位としてremを使うと超絶便利 - Qiita](http://qiita.com/butchi_y/items/453654828d9d6c9f94b0) でもあるようにレスポンシブなサイトを作るときに px と rem を使い分けると作りやすいみたいです。
ただ、remは `360px = 22.5rem` など、計算が必要になってくるので sass のfunctionを作ってしまいます。

``` sass
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

``` sass
max-width: px_to_rem(826px) // 51.625rem
```

のように rem を利用したサイズ指定が利用しやすくなります。

# 細かいTips
## 全体的に色を揃える
色を決めると、リンクの色が青になっていて目立ったりしてしまうので、テーマの色(今回は黒)にして下線を常に出してリンクとわかるようにしました。
## Panelを使わない
何かと情報をまとめる時に[Panel](http://getbootstrap.com/components/#panels)を使っていたんですが、Bootstrap v4 からは消えてしまう機能なのでなるべく使わずに。今回はh2を利用してどこまでが情報のまとまりかをわかるように表示。
## tableには交互に色をつける
これはBootstrapの機能をはずしたので

``` sass
tr:nth-child(even)
  background-color: #F2F2F2
```

のように対応。

## 単位は小さく

15人や10%の "人" や "%" の **単位部分を小さくすることで、数字(データ)が見やすくなる**。

## DT(項目名)は目立たなくさせる

"開催日時" などの **項目名を表す部分は、必要ではあるけど実際の情報に注目して欲しいのであくまで補助という形で色を薄く** しました。

## 左寄せと、右寄せは意識して使い分ける

例えば、金額などを表示するときには、桁数を揃えて表示したいので右寄せを利用する。
中央寄せを利用していて、タイトルなどの目立たせたいところは他と差異を作るために、そこだけ左寄せを利用する。

## 想定するフォームの幅にする

これは、まだ未実装ですがBootstrapのフォームを使っていると `width: 100%` のスタイルが適用されるので、全体的に間延びした印象を与えてしまうので、ちゃんと想定する入力の長さを決めて幅を整える。

## 削除のリンクなど取り扱いに注意が必要なものは色を目立つように

画面一覧の画像にはないですが、削除するためのリンクなどは、利用者に注意を促す意味もあり、普通のリンクと色を変える。

# 最後に

今回の紹介は以上です。他のサイトでもBootstrapを外してみたり、今回実装したサイトでも何かさらに進展があったら加筆していきます。
デザインをデザイナーの方に頼らず、ある程度構築できるようになりたいですね。