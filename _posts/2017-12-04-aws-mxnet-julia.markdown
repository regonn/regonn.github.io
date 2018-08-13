---
layout: post
title: "AWS EC2 に Julia 開発環境を構築し MXNet.jl でGPU処理したい"
date: 2017-12-04 9:41:28 +0900
categories: Kaggle
---

田中 TOM という名前で、データサイエンスコンペティションサイトの Kaggle の問題に挑戦する YouTube 動画を投稿してます。

[田中 TOM \- YouTube](https://www.youtube.com/channel/UCWXXSB94_CUAYD7XgdLzvBg)

普段 Julia 言語を使って解析をしていて、AWS で Amazon が公式にサポートしている MXNet を GPU 使って処理してみたかったのでチャレンジ。

実は、既に MXNet.jl を AWS で動かして記事にしてました。

[AWS の Deep Learning AMI を使って EC2 インスタンス上で 最新の Julia を動かせるように](https://tech-regonn.github.io/julia/2017/11/11/aws-julia.html)

けど、この記事を書いた後に使っていた Deep Learning AMI が大幅に変更された。

[AWS Deep Learning Conda と Base AMI の利用開始について \| Amazon Web Services ブログ](https://aws.amazon.com/jp/blogs/news/getting-started-with-the-aws-deep-learning-conda-and-base-amis/)

そして、Julia 0.6.1 だとインストールが失敗するため`Pkg.build('MXNet')` してビルドして使っていた [MXNet\.jl](https://github.com/dmlc/MXNet.jl) も v0.3.0 がリリースされてインストールできるようになったぽい。

なので環境構築を最初からやり直して、AWS 上で動かせるようにして、サンプルコードを使って CPU 処理と GPU 処理でどれだけ速さが違うのかも確かめてみる。

## 利用する EC2 環境

- インスタンスタイプ:  p2.xlarge(GPU を利用するため)
- AMI: Deep Learning AMI with Conda (Ubuntu) - ami-f0725c95

## サーバーへ ssh ログイン

最後の `-L` オプションを使うことで、サーバーの 8888 ポートをローカルとしてつかえるので jupyter notebook を使う場合に便利。

```shell
$ ssh -i your-key.pem ec2-user@ec2-XXX-XXX-XXX-XXX.compute-1.amazonaws.com -L 8888:localhost:8888
```

## Julia のインストール

Ubuntu だと最新の Julia 0.6.1 がインストールできないのでビルドする。最初から必要なライブラリ系はだいたい揃ってるみたい。並列処理で make するけど、それでも結構時間かかる。

```shell
$ sudo su -
$ apt-get update
$ apt-get install libpango1.0-0 -y
$ add-apt-repository ppa:staticfloat/julia-deps -y
$ apt-get update
$ cd /usr/local/src
$ git clone https://github.com/JuliaLang/julia.git
$ cd julia
$ git checkout v0.6.1
$ echo "JULIA_CPU_TARGET=core2" &gt; Make.user
$ make -j 4 julia-deps
$ make -j 4
$ make install
$ ln -s /usr/local/src/julia/julia /usr/local/bin/julia
```

## MXNet.jl のインストール

`julia` コマンドで起動してパッケージをインストール。以前は MXNet がインストール失敗してたけど、バージョンアップでインストールできるようになった。

```julia
$ julia
> Pkg.add("IJulia")
> Pkg.add("MXNet")
```

## Jupyter notebook で CPU と GPU を比較

`jupyter notebook` を実行して、MXNet.jl のサンプルコードで MNIST のデータを処理する。先に CPU でやってみて、後半 GPU。変更部分は `context=mx.gpu(0)` の部分。

<amp-gist
  data-gistid="145b1f441a34f31246b9ad0d650d3738"
  layout="fixed-height"
  height="225">
</amp-gist>

## CPU と GPU の比較結果

CPU だと 1epoch の処理に 100 秒かかっているのが、GPU だとなんと 1 秒に。パネェ。
