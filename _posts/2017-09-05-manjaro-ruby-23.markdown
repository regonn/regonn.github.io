---
layout: post
title: "Manjaro Linux で rbenv 2.3 系のインストールができない場合の対処法"
date: 2017-09-05 9:41:28 +0900
categories: manjaro
---

Manjaro & rbenv で rubyの2.4系はインストールできるけど、2.3系がコアダンプしてしまいインストールができないときの対処法メモ。

# 参考
https://github.com/rbenv/ruby-build/issues/1092

# 手順
gccが最新の7系でエラーになっているみたいなので、5系もインストールする。

`> sudo pacman -S gcc5`

あとは、rbenvインストール時にその `gcc-5` を使うようにする。(pacman のパッケージ名は gcc5 でコマンドは gcc-5 なので注意)

```
> CC=/usr/bin/gcc-5 \
PKG_CONFIG_PATH=/usr/lib/openssl-1.0/pkgconfig \
rbenv install 2.3.4
```

無事にインストールできた。
