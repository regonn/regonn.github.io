---
layout: post
title: "Kaggle の Kernel が動いている Julia Docker を最新版にしていく"
date: 2017-12-19 9:41:28 +0900
categories: Julia
---

[Julia Advent Calendar 2017](https://qiita.com/advent-calendar/2017/julialang)の19日目の記事です。
普段はデータサイエンスに関する動画を投稿しています。[田中TOM \- YouTube](https://www.youtube.com/channel/UCWXXSB94_CUAYD7XgdLzvBg)
最近データサイエンスのコンペティションサイトのKaggleで遊んでいて、Julia言語でデータサイエンス系の処理をやっている身としては、JuliaのKernelを登録したい。

しかし、Juliaでの登録自体はできるものの、エラーが起きてしまいコードが実行されない、そのため殆どのコンペティションではJuliaのKernelが登録されていないのが現状。

Kaggleのgithubページを見ると、[Kaggle/docker\-python: Kaggle Python docker image](https://github.com/Kaggle/docker-python) や [Kaggle/docker\-rstats: Kaggle R docker image](https://github.com/Kaggle/docker-rstats) といったPythonやR言語向けKernel dockerはちゃんとメンテナンスされているっぽい。

同じようにjuliaも [Kaggle/docker\-julia](https://github.com/Kaggle/docker-julia) でリポジトリは存在しているが、残念ながらメンテナスがされていない。

このままだと世界中のJulia愛好家がKaggleで活躍できないので、今回はローカルDockerfileを実行しつつ最新のJuliaの安定版が起動するように変更していく。そしていずれ、JuliaのKernelがKaggle上で動いてくれて、メンテも継続されていくのが夢。

## 元のDockerファイル

```
# kaggle/julia dockerfile

FROM ubuntu:16.04


ADD package_installs.jl /tmp/package_installs.jl

RUN  apt-get update && \
     apt-get install git software-properties-common curl wget libcairo2 libpango1.0-0 -y && \
     add-apt-repository ppa:staticfloat/julia-deps -y && \
     apt-get update -y && \
     apt-get install -y libpcre3-dev build-essential && \
     apt-get install -y gettext hdf5-tools && \
     apt-get install -y gfortran python && \
     apt-get install -y m4 cmake libssl-dev && \
     cd /usr/local/src && git clone https://github.com/JuliaLang/julia.git && \
     cd julia && git checkout v0.6.0 && \
     # Use generic instruction set; see https://github.com/JuliaLang/julia/pull/6220
     #   and https://groups.google.com/forum/#!topic/julia-dev/Eqp0GhZWxME
     echo "JULIA_CPU_TARGET=core2" > Make.user && \
     make -j 4 julia-deps && make -j 4 && make install && \
     ln -s /usr/local/src/julia/julia /usr/local/bin/julia

ENV JULIA_PKGDIR /root/.julia/v0.6

RUN julia /tmp/package_installs.jl

# IJulia
RUN   apt-get update && apt-get install -y python3-pip python3-dev && pip3 install jupyter && \
        julia -e "Pkg.add(\"Nettle\")" && \
        julia -e "Pkg.add(\"IJulia\")" && \
        julia -e "Pkg.build(\"IJulia\")" && \
# Make sure Jupyter won't try to migrate old settings
        mkdir -p /root/.jupyter/kernels && \
        cp -r /root/.local/share/jupyter/kernels/julia-0.6 /root/.jupyter/kernels && \
        touch /root/.jupyter/jupyter_nbconvert_config.py && touch /root/.jupyter/migrated && \
        julia -e "Base.compilecache(\"IJulia\")" && \
        julia -e "Base.compilecache(\"ZMQ\")" && \
        julia -e "Base.compilecache(\"Nettle\")"

RUN julia -e "Base.compilecache(\"BinDeps\")" && \
    julia -e "Base.compilecache(\"Cairo\")" && \
    julia -e "Base.compilecache(\"Calculus\")" && \
    julia -e "Base.compilecache(\"Clustering\")" && \
    julia -e "Base.compilecache(\"Compose\")" && \
    julia -e "Base.compilecache(\"DataArrays\")" && \
    julia -e "Base.compilecache(\"DataFrames\")" && \
    julia -e "Base.compilecache(\"DataFramesMeta\")" && \
    julia -e "Base.compilecache(\"Dates\")" && \
    julia -e "Base.compilecache(\"DecisionTree\")" && \
    julia -e "Base.compilecache(\"Distributions\")" && \
    julia -e "Base.compilecache(\"Distances\")" && \
    julia -e "Base.compilecache(\"GLM\")" && \
    julia -e "Base.compilecache(\"HDF5\")" && \
    julia -e "Base.compilecache(\"HypothesisTests\")" && \
    julia -e "Base.compilecache(\"JSON\")" && \
    julia -e "Base.compilecache(\"KernelDensity\")" && \
    julia -e "Base.compilecache(\"Loess\")" && \
    #julia -e "Base.compilecache(\"Lora\")" && \
    julia -e "Base.compilecache(\"MLBase\")" && \
    julia -e "Base.compilecache(\"MultivariateStats\")" && \
    julia -e "Base.compilecache(\"NMF\")" && \
    julia -e "Base.compilecache(\"Optim\")" && \
    julia -e "Base.compilecache(\"PDMats\")" && \
    julia -e "Base.compilecache(\"RDatasets\")" && \
    julia -e "Base.compilecache(\"SQLite\")" && \
    julia -e "Base.compilecache(\"StatsBase\")" && \
    julia -e "Base.compilecache(\"TextAnalysis\")" && \
    julia -e "Base.compilecache(\"TimeSeries\")" && \
    julia -e "Base.compilecache(\"ZipFile\")" && \
    julia -e "Base.compilecache(\"Gadfly\")" && \
    julia -e "Base.compilecache(\"MLBase\")" && \
    julia -e "Base.compilecache(\"Clustering\")" && \
    julia -e "using IJulia"

CMD ["julia"]
```

コード自体は以前ブログで書いた[AWS EC2 に Julia 開発環境を構築し MXNet\.jl でGPU処理したい](https://kaggle.regonn.tokyo/2017/12/04/aws-ec2-%e3%81%a7-julia-%e3%82%92%e6%a7%8b%e7%af%89%e3%81%97%e3%81%a6-mxnet-jl-%e3%82%92-gpu-%e4%bd%bf%e3%81%a3%e3%81%a6%e8%a8%88%e7%ae%97%e3%81%97%e3%81%9f%e3%81%84/)時にEC2のUbuntuインスタンスでJuliaのv0.6.1を構築するときに参考にして、動かせたので問題なさそう。

12月14日にJuliaの[v0.6.2](https://github.com/JuliaLang/julia/releases/tag/v0.6.2)がリリースされていたので、最新版にしてあげる。

```diff
-cd julia && git checkout v0.6.0 && \
+cd julia && git checkout v0.6.2 && \
```

## docker build 実行
とりあえずバージョンは上げたので `docker build` をする。結構長い間メンテされてないしエラーになると思ったらやっぱりエラーで止まる。

```
make[2]: *** [getarch_2nd] Error 1
Makefile:123: *** OpenBLAS: Detecting CPU failed. Please set TARGET explicitly, e.g. make TARGET=your_cpu_target. Please read README for the detail..  Stop.
*** Clean the OpenBLAS build with 'make -C deps clean-openblas'. Rebuild with 'make OPENBLAS_USE_THREAD=0' if OpenBLAS had trouble linking libpthread.so, and with 'make OPENBLAS_TARGET_ARCH=NEHALEM' if there were errors building SandyBridge support. Both these options can also be used simultaneously. ***
/usr/local/src/julia/deps/blas.mk:111: recipe for target 'scratch/openblas-85636ff1a015d04d3a8f960bc644b85ee5157135/build-compiled' failed
make[1]: *** [scratch/openblas-85636ff1a015d04d3a8f960bc644b85ee5157135/build-compiled] Error 1
```

## エラー対処
OpenBLASという行列演算ライブラリでエラーがでてた。
`TARGET=your_cpu_target` してねって書いてあったから、`echo "TARGET=core2" > Make.user` を追加してみたけど、同じエラーになってしまった。無念。。。

調べてv0.4の時に似たようなエラーに遭遇している人がいて、この場合OPENBLAS_TARGET_ARCHを設定したらビルドが通ったみたい。
[julialang v0\.4 \- 書いたものなど](http://machakann.hatenablog.com/entry/2015/10/13/005350)

エラー文にも書いてあった、`echo "OPENBLAS_TARGET_ARCH=NEHALEM" > Make.user &&` を加えたら、ちゃんとdocker buildが通った。

`NEHALEM`を指定しているけど、この[エラー文はJulia側で出しているみたい](https://github.com/JuliaLang/julia/blob/154e50356eb74ee8140fa7a6f6c81caa86da5a52/deps/blas.mk#L89)。

## プルリク作成
docker build も通ったのでプルリクを出してみたら、30分ぐらいでマージされた。
[minor update julia and pass build by regonn · Kaggle/docker\-julia](https://github.com/Kaggle/docker-julia/pull/4)
対応の速さからして、別にKaggleもJuliaのサポートを打ち切っているのではなく、ただ人的なリソースが足りてないっぽい？(そういうことを打ち切られていると呼ぶかもしれないけど)

これで、KaggleでJuliaのKernelが動くかといえば、まだ先は長いと思う。(ライブラリがちゃんとv0.6系に対応してなかったりしてるし、docker imageでubuntu使ってるけどjulia公式のimage使えばいいのではと思ってる。)
けど、この記事をきっかけにKaggleのJulia dockerに関わってくれる人が増えてくれたら嬉しいです。
ちゃんとDockerがメンテナンスされてきたら、Kaggle側も対応してくれると期待してます。
