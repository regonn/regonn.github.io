---
layout: post
title: "Ruby X Elixir Conf Taiwan 2018 Rubyデータサイエンス最前線"
date: 2018-05-04 9:41:28 +0900
categories: ruby
eyecatch_file: 'ruby_elixir_conf_taiwan.jpg'
---

台湾のRubyとElixirのTechイベント[Ruby X Elixir Conf Taiwan 2018](https://2018.rubyconf.tw/)に参加してきました。
自分にとって初めての海外一人旅と海外Techカンファレンス。

[趣味でデータサイエンス系の技術を触っていて](https://www.youtube.com/channel/UCWXXSB94_CUAYD7XgdLzvBg)データサイエンスに興味があり、丁度イベントでRubyのデータサイエンス界隈がどうなっているかについて、[pycall.rb](https://github.com/mrkn/pycall.rb)等のライブラリを作っている[@mrkn](https://twitter.com/mrkn)さん(株式会社Speee)のワークショップに参加したので、その内容をまとめようと思います。

内容のレベルはPythonで既に機械学習系のライブラリを触ったことのある人が対象ぐらいで書いています。

## ワークショップリポジトリ
[RubyData/workshop_taiwan_201804](https://github.com/RubyData/workshop_taiwan_201804)

今回のワークショップで使われたリポジトリ。

Dockerを用いて、それぞれの環境を作り進めていきました。

## Session 1: Introduction to Ruby's data tools ecosystem
現在のRuby界隈のDataScience系のプロジェクトやツールを紹介

### Ruby×データサイエンス系プロジェクト
- [SciRuby](http://sciruby.com/)
  - 科学技術計算におけるRuby環境の改善が目的の国際プロジェクト
  - Dataframe(Pythonでいうpandas)の [daru](https://github.com/SciRuby/daru) だったり、RubyをJupyter上で動かすための iruby 等を開発
- [Ruby Numo](https://github.com/ruby-numo/numo/blob/master/README.md)
  - 行列計算ライブラリ(Pythonでいうnumpy)の [NArray](https://github.com/ruby-numo/numo-narray) を開発
- [Red Data Tools](https://red-data-tools.github.io/ja/)
  - Rubyのデータ処理ライブラリを提供するプロジェクト
  - [Apache Arrow](https://arrow.apache.org/)のRubyバインドである[red-arrow](https://github.com/red-data-tools/red-arrow)を開発

### Jupyter Notebook上でRubyを動かす
#### [Session1.ipynb](http://nbviewer.jupyter.org/github/RubyData/workshop_taiwan_201804/blob/master/Session1.ipynb)
- Kaggle でもチュートリアルとして有名な [Titanic生存者データ](https://public.opendatasoft.com/explore/dataset/titanic-passengers/export/)
- [rbplotly](https://github.com/ash1day/rbplotly)(いい感じにデータを描画してくれるサービス[plotly](https://plot.ly/)をrubyから呼べるgem)を使ってチャートを表示
- CSVデータからDaruでデータフレーム型に変換して、rubyの文法でデータを処理できて、チャート表示までが問題なく出来ていた
- ある程度の前処理等もRubyでできてしまえる可能性を感じた

## Session 2: Introduction to pycall.rb
RubyでPythonのライブラリやメソッドを呼べる [pycall.rb](https://github.com/mrkn/pycall.rb) について
#### [Session2.ipynb](http://nbviewer.jupyter.org/github/RubyData/workshop_taiwan_201804/blob/master/Session2.ipynb)
- iris(アヤメ)データをpandas(データフレーム),seaborn(描画),scikit-learn(PCA,SVC)を使って解析

#### [wine\_data\_viewer](https://github.com/RubyData/wine_data_viewer)
- pycall.rbを用いてRails上でwineデータを表示
- helperで `= render_dataframe @dataframe` みたいに書けるようにしてあって、自分のプロジェクトとかでも使えそう

## Session 3: Introduction to machine learning programming in Ruby
[mxnet.rb](https://github.com/mrkn/mxnet.rb)を使ってディープラーニングを行う

- [MXNet](https://mxnet.incubator.apache.org/)は最近AmazonとかMicrosoftが力を入れているDeepLearningライブラリ
- 色々な言語(Python,C++,Scala,Julia,Perl,R)に対応していて、MXNet.rbの開発を進めてRubyも対応言語に入れてもらうのが目標

#### [Session3.ipynb](http://nbviewer.jupyter.org/github/RubyData/workshop_taiwan_201804/blob/master/Session3.ipynb)
- MNIST(手書き文字)データを使ってMXNetでモデルを実装して学習
- 隠れ層2層の全結合モデル
- 10Epochトレーニングを行い、バリデーションデータで正答率97%位
- トレーニング済みのモデルでランダムに画像10個をpredictしても正しくできていた
- より複雑な Wide ResNet のMXNet.rb実装デモもあった

## ワークショップを終えて
普段趣味でJulia言語を用いてデータサイエンスをやっていますが、仕事で使っているRuby(主にRails)でも色々できることが増えてきて、試しに使ってみる位のレベルになってきている気がします。

今後の話ですが、[@mrkn](https://twitter.com/mrkn)さんは科学計算向けプログラミング言語のJuliaもRubyとバインディングできるように進めているので、私もお手伝いできるように現在必死にRubyのC拡張を触ったりしてます。今後もそこら辺の記事を書けたらなと思います。