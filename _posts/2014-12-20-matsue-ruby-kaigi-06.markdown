---
layout: post
title: "松江Ruby会議06で出された問題を解いてみた"
date: 2014-12-20 9:41:28 +0900
categories: ruby
---

[松江Ruby会議 06](http://matsue.rubyist.net/matrk06/)にお邪魔しました。そこで、ライブコーディング大喜利なるものに参加したので、回答したコードを共有しておきます。

※この大喜利は事前に問題が出されて、それを実際に当日ライブコーディングしていくという形でした。


# CSVのソート問題

以下のような名前とそれぞれの項目の得点を記したCSVファイルがあります。
rubyの得点を基準に降順に並べ替え、名前とrubyの得点を出力しなさい。
また、平均点を計算し降順に並べ替え、名前と平均点を出力しなさい。
（平均点が同じ場合はrubyの点数が高い方を先に表示すること）

[csvファイル](http://www.tm-21.co.jp/rubykaigi/ruby_1.csv)

``` csv
name,ruby,php,python,perl
一郎,100,40,70,80
二郎,60,90,80,10
三郎,90,60,60,60
四郎,80,70,70,80
```

``` ruby
require 'open-uri'
require 'csv'
# csvファイル取得先URL
url = 'http://www.tm-21.co.jp/rubykaigi/ruby_1.csv'

# -- ここにコードを記述してください。 --

# -- ここに出力をしてください。 --
```

## 私の回答

``` ruby
require 'open-uri'
require 'csv'

# csvファイル取得先URL
url = 'http://www.tm-21.co.jp/rubykaigi/ruby_1.csv'

# -- ここにコードを記述してください。 --
csv_table = CSV.new(open(url), headers: true).read #CSV::Tableを扱えるようにする
language_list = csv_table.headers[1..-1] #nameカラムは外す
csv_table['ave'] = csv_table.map{ |row| (language_list.inject(0){ |sum, n| sum + row[n].to_i }) / language_list.size} #Tableにaverageのカラムを追加してしまう

score_list = csv_table.map{ |row| [row['name'], row['ruby'].to_i, row['ave'].to_i] } # 必要な部分だけを取り出す
score_list.sort_by!{ |score| score[1] }.reverse.each{ |score| puts "#{score[0]},#{score[1]}"}

# 安定ソートを行い順序を崩さないままアベレージで並べるので同点でもrubyの点数が高いほうが先に表示される
i = 0
score_list.sort_by{ |score| [score[2], i += 1]}.reverse.each{ |score| puts "#{score[0]},#{score[2]}"}
# -- ここに出力をしてください。 --
```

とりあえず、Rubyの便利な機能を使いたかったので、CSV.newのところでheadersのオプションをしている所がポイント。

設定するのと、しないとでは結構面倒さが変わってくる。

``` ruby
CSV.new(open(url), headers: true).read.class => CSV::Table
CSV.new(open(url)).read.class => Array
```

CSV::Tableだと `csv_table['ruby'] => ["100", "60", "90", "80"]`のように扱えるので便利。

あと、安定ソートもポイント。変数は増えてしまうが、もとの順番を保ちながらソートできるので便利。

# スクレイピング問題

以下のアドレスにアクセスしてください。

http://www.tm-21.co.jp/rubykaigi/ruby_1.html

一見、みんなRubyに見えますが、実は仲間はずれがいます！
上記アドレスをRubyを使用して読込み、仲間はずれをさがしてください。
仲間はずれの理由と何番目のRubyかを出力してください。
※仲間はずれの理由に特に正解はありません。自由に考えてOKです。

``` ruby
require 'open-uri'

# スクレイピング先のURL
url = 'http://www.tm-21.co.jp/rubykaigi/ruby_1.html'

# -- ここにコードを記述してください。 --

# -- ここに出力をしてください。 --
```

## 私の回答

``` ruby
require 'open-uri'

# スクレイピング先のURL
url = 'http://www.tm-21.co.jp/rubykaigi/ruby_1.html'

# -- ここにコードを記述してください。 --
html = open(url).read
ruby_list = html.scan(/class=\"(.+)\".+(Ruby\d{2})/)
ruby_list.select{|ruby| ruby[0] != "ruby" }.each{ |ruby| puts "#{ruby[0]},#{ruby[1]}"}
# -- ここに出力をしてください。 --
```

問題のURLの先にはRubyと書かれたものがズラッと並んでいて、とりあえずhtmlのclassで他と異なるものを見つけたので、それを取得することに。
nokogiri gem とかを使わずに、 **男は黙って正規表現** で値を取ってみました。

けど、仲間はずれの理由に「classが'ruby'じゃないから」という理由は安易すぎて、他の回答者の人が「classで一回しか使われていないもの」をコードでうまく実装していて、なるほどなと思った。

# 他の人のコードを見て気づいたこと

自画自賛になってしまいますが、他の人よりも可読性は意識できたと思います。（1つの操作が１行に収まっていて、何をしているか説明できる）
大喜利なのに面白みがないのはダメな気もしますが。。。。

# おまけ
あと、私の担当でない問題で、結構気に入った回答ができたので書いておきます。

## 確率の問題

0回から13回までのいずれかの回数だけトランプのカードを引けます。
引いたカードが、すべてスペードのカードである場合の確率を返すプログラムを作成してください。

以下は、トランプの内容です。

トランプの枚数は、52枚です。
トランプの絵柄は、「スペード、クローバー、ハート、ダイヤ」の4つです。
絵柄ごとにカードは、13枚あります。
カードの種類は、「A 2 3 4 5 6 7 8 9 10 J Q K」の13種類です。

### 私の回答

``` ruby
def cal_only_spade(n)
  (1..13).to_a.combination(n).size / (1..52).to_a.combination(n).size
end

p cal_only_spade(13)
```

数学的に考えたら、「対象の事象/全体の事象」なので、これで行ける気がする。けど`(1..52).to_a.combination(n)`を使ってるので富豪プログラミングすぎる気がする。(意味的には1から52の数字のうちn個を取り出すすべての組み合わせ。※気になる人は`puts (1..52).to_a.combination(5).to_a`とかをやってみればいいと思います。)

# おまけのおまけ
一応大喜利だったので最後に一言

一度でいいから見てみたい、バグなくコードが動くとこ

お後がよろしいようで。
