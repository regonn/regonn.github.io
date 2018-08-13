---
layout: post
title: "Railsでコンソールからファイルをアップロードする方法"
date: 2015-07-12 9:41:28 +0900
categories: rails
---


ファイルアップロードのデバッグ作業をしているときに、何回もファイルアップロードが面倒だったのですが、
Railsのコンソールでアップロードできる方法を知ったので忘れないように共有しておきます。

Rack::Test::UploadedFileを使うと簡単にできました。

例えば `public/sample.jpg` というファイルがあるとします。

するとコンソール上で次のようにすることでアップロードできます。

```
> file = Rack::Test::UploadedFile.new("public/sample.jpg", "image/jpeg")
=> #<Rack::Test::UploadedFile:0x007fd769dfa140 @content_type="image/jpeg", @original_filename="sample.jpg", @tempfile=#<Tempfile:/var/folders/8l/nqm1cg1d7nv6hgsww_s04vxw0000gn/T/sample.jpg20150712-55981-1ora8cn>>

> Item.create(file: file, name: "sample file")
```

まとめて画像をローカルにアップロードしたい時とかも簡単に扱えるので便利ですね。
