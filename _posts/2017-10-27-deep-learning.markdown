---
layout: post
title: "Deep Learning 補足動画 行列内積の誤差逆伝播の式を導出"
date: 2017-10-27 00:21:28 +0900
categories: DeepLearning
youtube: p86RFYXvqxA
---

以前 田中TOM で取り上げた [ゼロから作るDeep Learning](http://amzn.to/2ySiyvb) に関する [動画](https://youtu.be/rNfhxDRFj_o) でAffineレイヤーの内積の誤差逆伝播を求める部分が省略されていたので、行列で微分する部分の説明を動画で録画してみました。

# Deep Learning 補足動画

この動画は [Deep Learning 第7回:誤差逆伝播を用いてニューラルネットワークを実装](https://youtu.be/rNfhxDRFj_o)
で出てきた、行列での微分部分の補足動画です。

![](/images/2017-10-27/affine-graph.svg)

この

![](/images/2017-10-27/math1.png)

について解説してく。

## 今回想定するモデル

今回はバイアス部分は除いて、単純に入力2個と次の層への出力3個で考える。

![](/images/2017-10-27/model.png)

![](/images/2017-10-27/math2.png)

![](/images/2017-10-27/math3.png)

ここで

![](/images/2017-10-27/math4.png)

となる。

## ①について

![](/images/2017-10-27/math5.png)

まで計算しておいて

![](/images/2017-10-27/math6.png)

を代入すると

![](/images/2017-10-27/math7.png)

よって示せた。

## ②について

![](/images/2017-10-27/math8.png)

同じように

![](/images/2017-10-27/math9.png)

少し複雑になるが

![](/images/2017-10-27/math10.png)

なので、ほとんどは0になってしまう。

![](/images/2017-10-27/math11.png)

よって示せた。

## 単純な数値計算例(値は結構適当)

![](/images/2017-10-27/math12.png)

![](/images/2017-10-27/math13.png)

![](/images/2017-10-27/model1.png)

もし、損失関数を計算して

![](/images/2017-10-27/math14.png)

として値が渡ってきたとしたら、

![](/images/2017-10-27/math15.png)