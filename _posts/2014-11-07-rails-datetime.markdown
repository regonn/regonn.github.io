---
layout: post
title: "Rails(Ruby)でnilの可能性もあるDatetime型を比較して、新しい方の値を取得する【テスト付き】"
date: 2014-11-07 9:41:28 +0900
categories: rails
---

# 困った内容
RubyでDatetimeの比較についてです。

次のような場合を想定します。

``` ruby
foo, hoge #どちらも、Datetimeかnilの可能性がある
```

この2つの変数を比較して、新しい方の値を取得したいです。
しかし、Google先生に`Ruby Time 比較`を聞いても。

`<=>演算子を使うと-1 0 1 nilを返してくれるよ！`

という結果しか出てきませんでした（私の検索力が低いせいもありますが。。。。）

欲しいのは **どちらが大きいかの情報** ではなく、 **大きかった方の値** だし、もし片方にnilが含まれていた場合だと

``` irb
> foo = DateTime.now
=> Tue, 04 Nov 2014 19:48:53
> hoge = nil
=> nil
> foo <=> hoge
=> nil
```

となりどちらがnilなのか分かりません。

さらに、普通の比較演算子でtrue falseでの結果を知ろうとしても

``` ruby
foo < hoge
# ArgumentError: comparison of DateTime with nil failed
```

となりnilが含まれているとエラーになってしまいます。

だからといって

``` ruby
if foo.present? and hoge.present?
  foo < hoge
end
```

みたいに書いていっても、最終的に値を取得するまでの条件分岐が複雑になりそうで嫌でした。

## ダメなコード
まず、nilの値で時間の比較をしないようにダミーの値を持たせて三項演算子で値を取得する方法を考えました。

``` ruby
bar = foo || DateTime.new(0) # bar = DateTime.nowの値
piyo = hoge || DateTime.new(0) # piyo = Thu, 01 Jan 0000 00:00:00 の値
bar > piyo ? bar : piyo # DateTime.nowの値
```

こうすればnilだったら、古い値が入るので一番新しい時間が取得できます。

けど、 **DateTime.new(0)なんて書き方見たことないし** (西暦0年1月1日が木曜日なのは分かった) **何かDateTimeを比較できる良いメソッドとかあるんじゃない？** と思って色々調べましたがDateTimeを扱えるメソッドには想定している結果を返してくれるメッソドはありませんでした。

## 良いコード
仕方がないのでRails出来る人に聞くとすぐに解決

``` ruby
[foo, hoge].compact.max
```

で新しい値が取得できるようになりました！
compactでnilを取り除き、maxで比較するのか。

これだと両方nilでもnilを返してくれるのでif文は返り値がnilかどうかの1回で済みました。

教えてもらった人に
**一つ一つを比較しようとしているのは、せっかくのRubyの特徴を活かせていない**
という教訓をもらいました。

この、たとえ2つの比較であっても視界を広くして配列として扱うって考え方なかなか身につかないな〜。

2014/11/06追記（コメントでテストの要望があったので書いてみました)

``` ruby
require 'date'

describe 'Compare dates' do
  let(:today) { DateTime.now }
  # DateTimeはDateのサブクラスなので数字の足し算・引き算は日にち単位で行われる
  # DateTime.yesterday とやってしまうとDate型になるので注意
  let(:yesterday) { today - 1 }
  subject { [date_A, date_B].compact.max }

  context 'nilを含んでいない' do
    context 'date_Aの方が古い' do
      let(:date_A) { yesterday }
      let(:date_B) { today }
      it { is_expected.to eq today }
    end

    context 'data_Bの方が古い' do
      let(:date_A) { today }
      let(:date_B) { yesterday }
      it { is_expected.to eq today }
    end

    context '両方同じ値' do
      let(:date_A) { today }
      let(:date_B) { today }
      it { is_expected.to eq today }
    end
  end

  context 'nilを含んでいる' do
    context 'date_Aがnil' do
      let(:date_A) { nil }
      let(:date_B) { today }
      it { is_expected.to eq today }
    end

    context 'date_Bがnil' do
      let(:date_A) { today }
      let(:date_B) { nil }
      it { is_expected.to eq today }
    end

    context '両方nil' do
      let(:date_A) { nil }
      let(:date_B) { nil }
      it { is_expected.to be nil }
    end
  end
end
```
