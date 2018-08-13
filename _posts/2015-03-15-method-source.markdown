---
layout: post
title: "Rails5から標準Gemになるmethod_sourceを触ってみた"
date: 2015-03-15 9:41:28 +0900
categories: rails
---

ついにRails5が使えるようになりましたね。

[Start Rails 5 development :tada: · rails/rails@f25ad07](https://github.com/rails/rails/commit/f25ad07f5ade46eb978fa82658463232d0247c65)

## 追加されたmethod_source gem

早速自分の環境にrails5でrails newしてみて、Gemfileを覗いてみると、普段見かけなかった `method_source` というGemが追加されていました。

どうやら標準でGemに追加されたみたいです。

[This week in Rails: tokens migrations, method\_source and more \| Riding Rails](https://weblog.rubyonrails.org/2015/1/16/This-week-in-Rails-tokens-migrations-method-source-and-more/)

## 取り込まれた経緯

取り込まれた経緯を見てみますと、どうやらDHHがRails console上で次のようにmethodが書かれている場所を取得しても

```irb
irb(main):001:0> User.first.method(:yo).source_location
  User Load (0.7ms)  SELECT  "users".* FROM "users"   ORDER BY "users"."id" ASC LIMIT 1
=> ["/Users/regonn/sample/app/models/user.rb", 23]
```

と表示されるだけ(メソッド名は気にしないでください)で **いちいちクリップボードにPathをコピーして該当のLineに飛ばないと行けないんだけど、もっとシンプルに出来るんじゃない？** というIssueを上げていました。
pryでもやりたいことはできたんですが(DHHも納得して一度Issueが閉じられた)、`method_source gem`があまりにも期待していている挙動だったので、このGemが採用されたみたいです。

## 実際に使ってみた

`method_source gem`を使うと次のようにメソッドの中身をコンソール上で見ることができます。

参照先のmodelファイル

``` ruby
class User < ActiveRecord::Base
  #Say Yo!
  #Yoを返すYo!
  #TODO push Yo API
  def yo
    if self.feel == 'confortable'
      "Yo"
    end
  end
end
```

`method_source` の機能を使ってみる

``` irb
irb(main):001:0> puts User.first.method(:yo).source
  User Load (1.0ms)  SELECT  "users".* FROM "users"   ORDER BY "users"."id" ASC LIMIT 1
  def yo
    if self.feel == 'confortable'
      "Yo"
    end
  end
=> nil
irb(main):002:0> puts User.first.method(:yo).comment
  User Load (0.6ms)  SELECT  "users".* FROM "users"   ORDER BY "users"."id" ASC LIMIT 1
#Say Yo!
#Yoを返すYo!
#TODO push Yo API
=> nil
```

こんな感じでコメントも見ることができるのでいいですね。けど、このgemを使うならもう巨大なmethodは作れないですね。(methodは細かくするのが理想ですが)
