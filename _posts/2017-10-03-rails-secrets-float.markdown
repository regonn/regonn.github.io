---
layout: post
title: "Rails config/secrets.yml の環境変数設定値がfloat型で読み込まれてしまう際の対処法"
date: 2017-10-03 9:41:28 +0900
categories: rails
---

# 困ったこと
Railsで Slack OAuth 認証を実装していたら、Slack の client_id が `4167427656.251487306806` のような `数字 + .(ドット) + 数字` の形式が使われてた。

client_id は環境変数として使いたかったため

```
SLACK_CLIENT_ID='4167427656.251487306806'
```
のように設定し secrets.yml には次のように書く

``` config/secrets.yml
shared:
  client_id: <%= ENV['SLACK_CLIENT_ID'] %>
```

そして、Railsで値を呼ぼうとすると

```
> Rails.application.secrets.client_id
=> 4167427656.2514873
```

のように設定した値と異なって表示されてしまう。

# 原因

型を確認してみると

```
> Rails.application.secrets.client_id.class
=> Float
```
となっていて、Float型で読み込まれてしまっており、浮動小数点数の精度の影響で期待した値にならなかった。

# 解決策

String型で読み込めるように書いてあげる。

``` config/secrets.yml
shared:
  client_id: "<%= ENV['SLACK_CLIENT_ID'] %>"
```

こうすることで正しく文字として呼び出せる。

```
> Rails.application.secrets.client_id
=> "4167427656.251487306806"
```

あんまり、このような書き方で環境変数を呼んだことがなかったのでハマってしまった。
他の書き方で良さそうなものがありましたら教えてください。
