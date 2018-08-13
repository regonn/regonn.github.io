---
layout: post
title: "HTMLで画像に説明(Caption)を付けるならfigureタグを使えばよかった"
date: 2014-07-06 9:41:28 +0900
categories: html
---

画像の下に説明（Caption）を加えたいのでimgとpタグを使ってスタイルシートで編集していましたが、そんなことせずにfigureタグが用意されていました。

``` html
<p>黄色あなたは黄色</p>
<p>植物あなたは植物</p>
<figure id="banana">
<img src="../images/banana.jpg" alt="バナナは好きかい？">
<figcaption>美味しいバナナ</figcaption>
</figure>
<p>そう、あなたはバナナ</p>
```

みたいに書けば、こうなります。

![figure.png](/images/2014-07-06.png)

ちゃんとHTMLもドキュメント読まないといけませんね。
