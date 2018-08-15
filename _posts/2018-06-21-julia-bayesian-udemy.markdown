---
layout: post
title: "ベルヌーイ分布の確立密度関数をJuliaで計算する"
date: 2018-06-21 12:00:00 +0900
categories: Julia
---

ベイズを学ぶために Udemy の講座で

<a href="https://px.a8.net/svt/ejp?a8mat=2ZJCFL+GE0L9U+3L4M+BW8O2&a8ejpredirect=https%3A%2F%2Fwww.udemy.com%2Fpythonstan%2F%3Fdeal_code%3DJPA8DEAL2PERCENTAGE%26aEightID%3Ds00000016735001" target="_blank" rel="nofollow">【Python と Stan で学ぶ】仕組みが分かるベイズ統計学入門</a>

<amp-img src="https://www18.a8.net/0.gif?a8mat=2ZJCFL+GE0L9U+3L4M+BW8O2" alt="" width="1px" height="1px" layout="fixed" ></amp-img>

の Python のコードを Julia で書き直して勉強している。

Python のコード

次のようにベルヌーイ分布で data から N_data 個取った場合の確立密度関数を計算する Python コードがあった

```python
import from scipy.stats import bernoulli

p_a = 0.3
data = [0,1,0,0,1,1,1]
N_data = 2
likehood_a = bernoulli.pmf(data[:N_data], p_a)
likehood_a # array([ 0.7, 0.3])
```

Julia で書き直すとこんな感じになった

```julia
using Distributions

p_a = 0.3
data = [0,1,0,0,1,1,1]
N_data = 2
likehood_a = pdf(Bernoulli(p_a), data[1:N_data])
likehood_a
# 2-element Array{Float64,1}:
# 0.7
# 0.3
```

Julia で[Distributions.jl](https://github.com/JuliaStats/Distributions.jl)には pmf メソッドは用意されていなかったので pdf を変わりに使っている。
Julia 贔屓な目で見てしまうが、それでも Julia の書き方のほうが `Bernoulli(p_a)` とか使うあたりがメソッドを汎用性高く使えている気がする。
もちろん書き方はライブラリにもよるのだけども。
