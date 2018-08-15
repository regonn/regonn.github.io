---
layout: post
title: "Juliaの勉強会へ参加"
date: 2017-11-03 17:53:28 +0900
categories: Julia
---

最近 Julia のデータサイエンス本を購入。

[Julia データサイエンス―Julia を使って自分でゼロから作るデータサイエンス世界の探索](https://amzn.to/2nEyb2o)

<a href="https://www.amazon.co.jp/Julia%E3%83%87%E3%83%BC%E3%82%BF%E3%82%B5%E3%82%A4%E3%82%A8%E3%83%B3%E3%82%B9%E2%80%95Julia%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E8%87%AA%E5%88%86%E3%81%A7%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E4%BD%9C%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%82%B5%E3%82%A4%E3%82%A8%E3%83%B3%E3%82%B9%E4%B8%96%E7%95%8C%E3%81%AE%E6%8E%A2%E7%B4%A2-Anshul-Joshi/dp/486043501X/ref=as_li_ss_il?&linkCode=li3&tag=regonns-22&linkId=3561d4cdb45e7ef826066ba59d2879a0&language=ja_JP" target="_blank"><amp-img src="https://ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=486043501X&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=regonns-22&language=ja_JP" alt="" width="177px" height="250px" layout="fixed" ></amp-img></a>

<amp-img src="https://ir-jp.amazon-adsystem.com/e/ir?t=regonns-22&language=ja_JP&l=li3&o=9&a=486043501X" width="1px" height="1px" alt="" layout="fixed" ></amp-img>

ちょうど、この書籍に関する勉強会が開催されるのを知り参加してみる。

[Julia データサイエンスワークショップ \- connpass](https://data-refinement.connpass.com/event/70804/)

やはりというか、書籍で書かれている Julia のバージョンが v0.4 で現在は v0.6 なので、動かないコードが多い印象。(公式 github は更新がなく、日本の出版社も v0.6 対応は近日公開になっている。。。)発表者の方が、一部 v0.6 でも動くようにしてもらっていたので助かった。

[data\-refinement/Julia\-for\-Data\-Science](https://github.com/data-refinement/Julia-for-Data-Science)

この勉強会を開催するに至った経緯に次の本の話があり、この本の著者が公開しているサンプルコードは Julia のコードで書かれているみたいで、今後も Julia は科学計算分野で使われていきそう。

[機械学習スタートアップシリーズ ベイズ推論による機械学習入門 (KS 情報科学専門書)](https://amzn.to/2KTohmQ)

<a href="https://www.amazon.co.jp/%E6%A9%9F%E6%A2%B0%E5%AD%A6%E7%BF%92%E3%82%B9%E3%82%BF%E3%83%BC%E3%83%88%E3%82%A2%E3%83%83%E3%83%97%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%99%E3%82%A4%E3%82%BA%E6%8E%A8%E8%AB%96%E3%81%AB%E3%82%88%E3%82%8B%E6%A9%9F%E6%A2%B0%E5%AD%A6%E7%BF%92%E5%85%A5%E9%96%80-KS%E6%83%85%E5%A0%B1%E7%A7%91%E5%AD%A6%E5%B0%82%E9%96%80%E6%9B%B8-%E9%A0%88%E5%B1%B1-%E6%95%A6%E5%BF%97/dp/4061538322/ref=as_li_ss_il?&linkCode=li3&tag=regonns-22&linkId=b9bc8828dfd06fa48a0c42cd87c57ef8&language=ja_JP" target="_blank"><amp-img src="https://ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4061538322&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=regonns-22&language=ja_JP" alt="" width="177px" height="250px" layout="fixed" ></amp-img></a>

<amp-img src="https://ir-jp.amazon-adsystem.com/e/ir?t=regonns-22&language=ja_JP&l=li3&o=9&a=4061538322" width="1px" height="1px" alt="" layout="fixed" ></amp-img>

[「機械学習スタートアップシリーズ ベイズ推論による機械学習入門」のサンプルコード](https://github.com/sammy-suyama/BayesBook)(コチラは最新の v0.6 で書かれている)

最初に紹介した Julia のデータサイエンス本の次はこれを読んでいくのと、勉強会で Julia で書かれている(他の言語でのライブラリの API を叩いていない)deep learning ライブラリの  [hshindo/Merlin\.jl](https://github.com/hshindo/Merlin.jl)(国産ライブラリ) や  [pluskid/Mocha\.jl](https://github.com/pluskid/Mocha.jl) の存在を知ったので触っていこうと思う。
