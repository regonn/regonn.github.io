---
layout: post
title: "Kaggle Knight Matuse #4 でやったこと Julia言語でラベル毎に画像を保存する"
date: 2018-04-19 9:41:28 +0900
categories: julia
---

2018/04/18 開催の [Kaggle Knight Matsue #4](https://www.facebook.com/events/247495035794663/) でやった作業内容。

Kaggleの[iMaterialist Challenge](https://www.kaggle.com/c/imaterialist-challenge-furniture-2018)の作業

## 画像取得スクリプトの改善

[以前作った画像取得スクリプト](/julia/2018/03/30/julia-download-images.html) だと、ラベルの情報が無かったり、先にフォルダを作っておかないとエラーになったりしたので、自動で取得できるように変更する。

### スクリプトの仕様
- ラベル情報を読み取り、ラベルのフォルダに画像を保存
- フォルダが存在していなければ、新しく `mkdir` でフォルダ作成
- 全ての画像を取得するのではなく、それぞれのラベルの画像をn枚毎ダウンロードしたかったので、ラベル毎の枚数を `length(readdir(dirname)` でファイル数を読み取って、条件に追加する(コードだと100枚にしてある)
- label に `missing` が存在していたので、その場合は保存しないようにする
- json ファイルのときは for in で処理出来ていたが、Dataframeの場合は eachrow を使って行毎に処理できるようにする

```julia
for row in eachrow(target_rows)
    try
        dirname = "./images/$(row[:label_id])"
        filename = "./images/$(row[:label_id])/$(row[:image_id]).jpg"
        if !isdir(dirname)
            mkdir(dirname)
        end
        if !(ismissing(row[:label_id]).|(isfile(filename)).|(length(readdir(dirname)) > 100))
            println(filename)
            t = tempname()
            download(row[:url], t)
            img = load(t)
            square = imresize(img, (80, 80))
            save(filename, square)
            rm(t)
        end
    catch err
        println("ERROR: ", err)
    end
end
```

これで、あとは放置しておけば勝手に指定した枚数毎にラベルの画像がダウンロードできるようになった。途中で止まっても、ちゃんと枚数を数えてから、ダウンロードが再開されるので安心。