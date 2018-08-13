---
layout: post
title: "Julia DataFrames.jl で数字で始まるカラム名を取得する場合の工夫"
date: 2018-02-18 22:32:28 +0900
categories: Julia
---

[以前の記事](https://tech-regonn.github.io/julia/2018/02/16/julia-csv-dataframes.html) で紹介した

``` julia
DataFrame(load("./input/train.csv"))
```

だと、どうやら数字が先頭のカラム名をそのまま扱ってしまう。

[Julia DataFrame columns starting with number? \- Stack Overflow](https://stackoverflow.com/questions/34858166/julia-dataframe-columns-starting-with-number)

によると、 `:2aa` という表記は Julia 上シンボルではなく `1:2aa` というレンジの扱いになってしまうため、`"1st"`というカラムが存在しているからといって、`df[:1st]` と書いても想定しているカラムを取得できない。

DataFrame.readtable だと、いい感じにカラム名の先頭を `"1st" => "x1st"` のように `x` を入れてくれていた(これも、実際データ触る時邪魔な気もするけど)

## 解決方法
ちゃんとシンボルだと指定してあげればいいので、

``` julia
df[Symbol("1st")]
```

としてあげれば取得できる。少し不格好だが嫌いじゃない。