---
layout: post
title: "Juliaで並列計算を試す"
date: 2017-10-24 23:34:28 +0900
categories: Julia
---

引き続き Numerai をランダムフォレストで解いてみる。

https://www.slideshare.net/sfchaos/julia-39591233

のスライドによると、 DecisionTree は並列計算対応してくれているらしいので、実験してみた。

``` julia
using DataFrames
using DecisionTree
using ScikitLearn
using LossFunctions
train = readtable("./numerai_training_data.csv")
test = readtable("./numerai_tournament_data.csv")
yTrain_array = Array(train[:, :target] * 1.0)
xTrain_array = Array(train[:, 4:53])
@time model = build_forest(yTrain_array, xTrain_array, 2, 30, 4, 0.7, 50)
pred_test = apply_forest(model, Array(test[:,4:53]))
labelsInfoTest = DataFrame()
labelsInfoTest[:id] = test[:id]
labelsInfoTest[:probability] = pred_test
writetable("numerai_answer3.csv", labelsInfoTest, separator=',', header=true)
```

`@time` を付けることでそのコードでの処理時間やメモリ使用量が分かるっぽい。

## 実行結果

``` shell
> julia numerai.jl
657.770862 seconds

> julia -p 3 numerai.jl
282.813077 seconds
```

確かに、かなり時間が節約できている。

今後はJupyter である程度変数とかを絞ったら、コードにして並列計算したほうが良さそう。

肝心のNumeraiの結果は一度、Loglossが 0.70 台まで下がったが、Originarity チェックで弾かれた。なんでだろう。

他の結果は 0.75 以上の結果になってしまった。

損失関数の計算で分かってきたけど、1か0 の結果に対して、全ての予測が 0.5 だと、Logloss は 0.75 になる。

つまり0.75を下回らないと、予測の精度は全てを 0.5 で答えた結果よりも悪いことになる。

ここに一つのハードルがありそうだ。データはマスキングされているけど、元々はグラフデータとかの内容だろうし、普通に株取引とかで機械学習でやろうとしても、なかなか結果が出せないのと同じで、取引データから利益を出せるようになるには、もっとデータサイエンスを学ばないといけないな。