---
layout: post
title: "Kaggleで容量の大きいcsvファイルを取り扱うには？(Postgresql編)"
date: 2017-12-20 9:41:28 +0900
categories: kaggle
---

Kaggleをやってると大きめのCSVファイルデータが用意されていて処理に困ったので、今回はそれを Postgresql で処理して容量を減らして再びCSVにしていきます。

## 想定ファイル
今回は次のようなcsvを扱います。

### sample.csv
``` csv
uuid,name,price,date
xxxxxxxxxxxx,tomato,10,2017-10-19
yyyyyyyyyyyy,banana,8,2017-10-19
```

## Postgres に DB を作ってアクセスする
- 入力ファイルがあるディレクトリで作業を行う
- sampleという名前のDBを作成する
- 登録されているユーザー名(username)でsample DBにアクセスする

``` shell
$ ls # 入力ファイルがあるディレクトリで作業を行う
sample.csv

$ createdb sample

$ psql -d sample -U (username)
```

## Table を作る
- samplesという Table を作成する。csvと同じ構成になるようにカラムを設定する
- `\d`で作ったTableが存在するかを確認

``` postgresql
sample=# CREATE TABLE samples (
    uuid            varchar(80),
    name            varchar(80),
    price           int,
    date            date
);
sample=# \d
         List of relations
 Schema |  Name   | Type  | Owner
--------+---------+-------+--------
 public | samples | table | username
```

## ローカルのcsvをインポートする
- 今回は `\copy` でメタコマンドを使っているが、`COPY`コマンドを使う場合は絶対パスを指定してあげる
- `header` を追加することでcsvの最初の行を無視してくれる

``` postgresql
sample=# \copy samples from 'sample.csv' with csv header;
COPY 2
sample=# select * from samples;
     uuid     |  name  | price |    date
--------------+--------+-------+------------
 xxxxxxxxxxxx | tomato |    10 | 2017-10-19
 yyyyyyyyyyyy | banana |     8 | 2017-10-19
(2 rows)
```

## SQLの実行結果をcsvで出力する
- ローカルに `output.csv` が出力される

``` postgresql
sample=# \copy (SELECT uuid, name, price, date FROM samples WHERE price > 9) to 'output.csv' with csv delimiter ',' header;
```

### output.csv
``` csv
uuid,name,price,date
xxxxxxxxxxxx,tomato,10,2017-10-19
```

これで、sqlで処理して容量を減らしたcsvができるようになった。
