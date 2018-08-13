---
layout: post
title: "Julia で Numerai にチャレンジ"
date: 2017-10-22 9:52:28 +0900
categories: Julia
---

[Numerai](https://numer.ai/) というデータサイエンスが競い合って、効率の良いファウンドを運営しようという試み。

[ビットコインで雇われた匿名の7,500人が「頭脳」となるヘッジファンド「Numerai」｜WIRED\.jp](https://wired.jp/2017/02/26/numerai/)

良いデータを登録できると、報酬ももらえるので頑張って Julia で挑戦してみる。

``` julia
using DataFrames
using DecisionTree
using ScikitLearn

train = readtable("./numerai_training_data.csv")
test = readtable("./numerai_tournament_data.csv")

yTrain_array = Array(train[:, :target] * 1.0)
xTrain_array = Array(train[:,4:53])

model = RandomForestRegressor()
ScikitLearn.fit!(model, xTrain_array, yTrain_array)

predTest = ScikitLearn.predict(model, Array(test[:,4:53]))

labelsInfoTest = DataFrame()
labelsInfoTest[:id] = test[:id]
labelsInfoTest[:probability] = predTest

writetable("numerai_predict.csv", labelsInfoTest, separator=',', header=true)
```

とりあえず、simpleなランダムフォレストを作って登録もできた。Loglossは0.76522ぐらいだった。