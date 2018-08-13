---
layout: post
title: "Kaggle Knight Matuse #2 でやったこと Julia言語でHOGを用いてObject Detection"
date: 2018-04-06 9:41:28 +0900
categories: julia
youtube: 'qUVc8S3sCAg'
---
2018/04/04開催の [Kaggle Knight Matsue #2](https://www.facebook.com/events/247495035794663/) でやった作業内容。

[Object Detection using HOG · ImageFeatures](http://juliaimages.github.io/ImageFeatures.jl/latest/tutorials/object_detection.html)
を参考にして、Kaggleの[iMaterialist Challenge](https://www.kaggle.com/c/imaterialist-challenge-furniture-2018)準備。

Juliaで保存する前に画像の拡大縮小をするには次の記事が参考になった。

[Juliaで画像の拡大縮小を行う \- Qiita](https://qiita.com/nel215/items/ed3476a1a4607c3f9beb)

Juliaのコードは次のようになった。

```julia
# 必要なライブラリを設定
using JSON
using Images
using DataFrames
using FileIO
using LIBSVM # 学習に利用

# ./input にデータを置いてる
json = JSON.parsefile("./input/train.json")

# 数が大きいので最初の10件で試す。images => 画像URL, annotations => ラベル情報 が別々に入っている
a = json["images"][1:10]

# Json から Dataframe へ変換する。コピペコード。
ka = union([keys(r) for r in a]...)
df = DataFrame(;Dict(Symbol(k)=>get.(a,k,NA) for k in ka)...)
df[:url] = map(x-> x[1], df[:url])

# 毎回やるのは面倒なのでCSVで保存しておく
FileIO.save("images-1-10.csv", df, quotechar=nothing)

# 同じ作業を annotations でも行う
a = json["annotations"][1:10]
ka = union([keys(r) for r in a]...)
df = DataFrame(;Dict(Symbol(k)=>get.(a,k,NA) for k in ka)...)
FileIO.save("annotations-1-10.csv", df, quotechar=nothing)

# CSVデータを image_id で join して一つの DataFrameに
annotations = DataFrame(load("./annotations-1-10.csv"))
images = DataFrame(load("./images-1-10.csv"))
images_annotations = join(images, annotations, on = :image_id, kind= :left)

# 画像を 80x80 で保存する。 HOG で行う場合に cell_size の倍数にしないといけないらしい
for row in eachrow(images_annotations)
    try
        t = tempname()
        download(row[:url], t)
        img = load(t)
        square = imresize(img, (80, 80))
        save("./images/$(row[:label_id])/$(row[:image_id]).jpg", square)
        rm(t)
    catch err
        println("ERROR: ", err.msg)
    end
end

# 最初の画像10件は全てラベルは5なので、そこで処理していく
examples = "./images/5/"
n_example = length(readdir(examples))

data = Array{Float64}(2916, n_example)  # 80x80の場合 2916
labels = Vector{Int}(n_example)

for (i, file) in enumerate(readdir(examples))
    filename = "./images/5/$file"
    print(filename)
    img = load(filename)
    data[:, i] = create_descriptor(img, HOG())
    labels[i] = 5
end

random_perm = randperm(n_example)

# 分ける必要も無いけどサンプルデータのやり方を真似る
train_ind = random_perm[1:7]
test_ind = random_perm[8:9]

model = svmtrain(data[:, train_ind], labels[train_ind]);
img = load("./images/5/11.jpg") # 上のコードとは別にテスト用に 11.jpg も用意しておく
descriptor = Array{Float64}(2916, 1)
descriptor[:, 1] = create_descriptor(img, HOG())

predicted_label, _ = svmpredict(model, descriptor);
print(predicted_label)

# モデルの正答率をだすけど。ラベルが 5 しか用意していないので、100%になるので、ラベルを増やして試したい
predicted_labels, decision_values = svmpredict(model, data[:, test_ind]);
@printf "Accuracy: %.2f%%\n" mean((predicted_labels .== labels[test_ind]))*100
```