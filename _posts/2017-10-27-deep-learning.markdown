---
layout: post
title: "Deep Learning 補足動画 行列内積の誤差逆伝播の式を導出"
date: 2017-10-27 00:21:28 +0900
categories: DeepLearning
youtube: p86RFYXvqxA
---

[ゼロから作る Deep Learning](http://amzn.to/2ySiyvb) で Affine レイヤーの内積の誤差逆伝播を求める部分が省略されていたので、行列で微分する部分の説明を動画で録画してみました。

# Deep Learning 補足動画

行列での微分部分の補足動画です。

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/affine-graph.svg" alt="" width="934px" height="391px" layout="responsive" ></amp-img>

この

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math1.png" alt="" width="300px" height="182px" layout="fixed" ></amp-img>

について解説してく。

## 今回想定するモデル

今回はバイアス部分は除いて、単純に入力 2 個と次の層への出力 3 個で考える。

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/model1.png" alt="" width="960" height="720" layout="responsive" ></amp-img>

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math2.png" alt="" width="300px" height="127px" layout="fixed" ></amp-img>

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math3.png" alt="" width="1004" height="299" layout="responsive" ></amp-img>

ここで

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math4.png" alt="" width="300px" height="134px" layout="fixed" ></amp-img>

となる。

## ① について

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math5.png" alt="" width="300px" height="94px" layout="fixed" ></amp-img>

まで計算しておいて

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math6.png" alt="" width="300px" height="168px" layout="fixed" ></amp-img>

を代入すると

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math7.png" alt="" width="1179" height="446" layout="responsive" ></amp-img>

よって示せた。

## ② について

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math8.png" alt="" width="300px" height="91px" layout="fixed" ></amp-img>

同じように

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math9.png" alt="" width="747" height="175" layout="responsive" ></amp-img>

少し複雑になるが

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math10.png" alt="" width="1902" height="382" layout="responsive" ></amp-img>

なので、ほとんどは 0 になってしまう。

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math11.png" alt="" width="720" height="574" layout="responsive" ></amp-img>

よって示せた。

## 単純な数値計算例(値は結構適当)

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math12.png" alt="" width="300px" height="138px" layout="fixed" ></amp-img>

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math13.png" alt="" width="762" height="299" layout="responsive" ></amp-img>

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/model1.png" alt="" width="960" height="720" layout="responsive" ></amp-img>

もし、損失関数を計算して

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math14.png" alt="" width="300px" height="58px" layout="fixed" ></amp-img>

として値が渡ってきたとしたら、

<amp-img src="https://blog.regonn.tokyo/images/2017-10-27/math15.png" alt="" width="300px" height="157px" layout="fixed" ></amp-img>
