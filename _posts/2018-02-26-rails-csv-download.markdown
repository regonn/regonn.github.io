---
layout: post
title: "RailsでCSVダウンロード機能実装(Shift JIS対応)"
date: 2018-02-26 9:41:28 +0900
categories: rails
---

RailsでDBのレコードを読み込んでCSVとして吐き出す方法。

Userというモデルで実装しています。

以下のコードをダウンロードを行いたいControllerのアクションに追加(`config/routes.rb` にもアクションを追加してください。)

```rb
# app/controllers/users_controller.rb
def csv
  self.response.headers["Content-Type"] ||= 'text/csv; charset=Shift_JIS'
  self.response.headers["Content-Disposition"] = "attachment;filename=users_#{Time.current.to_i}.csv"
  self.response.headers["Content-Transfer-Encoding"] = "binary"
  self.response.headers["Last-Modified"] = Time.current.ctime.to_s

  self.response_body = Enumerator.new do |yielder|
    yielder << encode_sjis(User.csv_header)
    # ここで、出力するデータのスコープを変更できます。
    User.all.each do |user|
      yielder << encode_sjis(user.to_csv)
    end
  end
end

private

def encode_sjis data
  data.encode("Shift_JIS", invalid: :replace, undef: :replace, replace: '?')
end
```

モデルの方も実装していきます

```rb
# app/models/user.rb

def self.csv_header
  header = []
  header << (User.human_attribute_name :id)
  header << (User.human_attribute_name :name)
  header.to_csv
end

def to_csv
  cols = []
  cols << id
  cols << name
  cols.to_csv
end
```