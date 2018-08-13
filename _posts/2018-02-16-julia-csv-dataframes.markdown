---
layout: post
title: "Julia の DataFrames.readtable が deprecated になって CSV.read 推奨になったけど使いづらいので別の方法を探してみた"
date: 2018-02-16 23:07:28 +0900
categories: Julia
---

DataFramesでreadtableを実行しようとすると、deprecated warningが出る。

## DataFrames.readtable

``` julia
DataFrames.readtable("./input/train.csv")
=>
WARNING: readtable is deprecated, use CSV.read from the CSV package instead
```

## CSV.read

これに対応しようとして CSV の read メソッドを呼ぼうとするが、現状このメソッドでやろうとするとNull値を許可したり、Unionで型を指定してあげないといけなかったりする。

``` julia
CSV.read("./input/train.csv")
=>
CSV.ParsingException("error parsing a `Int64` value on column 27, row 235; encountered 'N'")
```

## CSVFiles.jl

他に良さそうなライブラリがないか探してみたら、[CSVFiles.jl: FileIO\.jl integration for CSV files](https://github.com/davidanthoff/CSVFiles.jl)が使いやすそうだった。そういえば、提出用ファイルで出力する時にheaderのカラムにダブルクォーテーションを使いたくないときにもこのライブラリで対応できた。スター数は全然付いていないがメンテもされているし使い勝手が良い。作者のdavidanthoffさんがjuliaの質問サイトとかで自分のライブラリを紹介して広めているのも健気で好感が持てる。[Read file with CSV\.read \- Usage / First steps \- Julia discourse](https://discourse.julialang.org/t/read-file-with-csv-read/7838/6)

使い方としては

``` julia
DataFrame(load("./input/train.csv"))
```

で `DataFrames.readtable` と同じように扱うことができる。