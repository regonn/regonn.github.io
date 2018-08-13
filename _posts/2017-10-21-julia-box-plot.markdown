---
layout: post
title: "Julia で箱ひげ図を表示する"
date: 2017-10-21 17:00:28 +0900
categories: Julia
---

Kaggle の [Titanic 問題](https://www.kaggle.com/c/titanic)  をやっている。

性別と等級から年齢の平均は異なりそうで、それを NaN 値に入れることを考えた。

まずは、実際にどれくらい違いがでるのかを確認してみる。

[箱ひげ図](https://ja.wikipedia.org/wiki/%E7%AE%B1%E3%81%B2%E3%81%92%E5%9B%B3) を表示できれば良さそうなので、Julia の Gadfly ライブラリを使って、図を表示してみる。

<amp-gist
  data-gistid="0388459c79930008aa0b2553d4964ae6"
  layout="fixed-height"
  height="225">
</amp-gist>

やっぱり差異はありそうなので、今後の `:Age` の `NaN` 値にはとりあえず、この平均を入れていくことにする。
