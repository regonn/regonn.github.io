---
layout: post
title: "RSpec + Capybaraでアラート/確認ダイアログを操作する場合は page.driver.browser.switch_to.alert.accept ではなく page.accept_confirm を使う"
date: 2017-06-13 9:41:28 +0900
categories: capybara
---

RSpecとCapybaraの環境でダイアログの操作を行いたい場合は、
「`page.driver.browser.switch_to.alert.accept` を使う」という説明をよく見かけます。

ですが、Capybaraには、

- `:accept_alert`
- `:accept_confirm`
- `:dismiss_confirm`
- `:accept_prompt`
- `:dismiss_prompt`

というダイアログ操作用のメソッドが用意されています。
なので、こちらを使うようにした方がよいです。

[Class: Capybara::Session — Documentation for jnicklas/capybara \(master\)](http://www.rubydoc.info/github/jnicklas/capybara/master/Capybara/Session)

## 上記メソッドを使うことのメリット
上記メソッドを使うと、ただ文字数減るだけでなく、他にもメリットが出てきます。

今後 Chrome Headless ブラウザでテストを実行する時には、このメソッドの中で呼ばれている`driver.accept_modal(:confirm)`という処理がHeadlessへの移行に必要な差分を吸収してくれるようです。

[Workaround chromedriver lack of support for system modals when headless by twalpole · Pull Request \#1859 · teamcapybara/capybara](https://github.com/teamcapybara/capybara/pull/1859/files)

あと、引数を渡すと、モーダルの内容確認してくれたり、指定した時間(デフォルトは`Capybara.default_max_wait_time`なので、設定しなくてもOK)待ってくれたりします。

今後 Chrome Headless へ移行する予定なら、先に対応しておくと楽になりそうです。

## 移行作業

既存のテストコードに対して、

``` diff
- page.driver.browser.switch_to.alert.accept
+ page.accept_confirm
```

にするだけでOKです。
