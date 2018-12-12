---
layout: post
title: "FXシステムトーレードツール jiji の Tensorflow サンプルを複数通貨対応 + Digital Ocean VPS(CentOS7) で動かす"
date: 2016-10-26 9:41:28 +0900
categories: jiji
---

# 目的

[TensorFlow を使った為替(FX)のトレードシステムを作るチュートリアル ～システムのセットアップからトレードまで～](http://qiita.com/jiji_platform/items/268377c542706e6f44b1) で公開されているサンプルコードを実行してみて、トレードの成績は良いものの、取引回数が減ってしまうので、複数通貨で取引できるようにしてみた。さらに今回は [Digital Ocean VPS](https://www.digitalocean.com/) で実際に動かすところまでやってみる。

※ 実際の取引は自己責任でお願いしますね。

# 取引 Agent のコード

サンプルコードを参考に複数通貨対応していった感じ。Tensorflow 側のコードはいじってないです。
ちょっとログを出力する関係でイマイチな部分もある。
Currency クラスを作ってそのインスタンスを複数持つことで対応した。

```ruby
# tensorflow_agent.rb
# coding: utf-8

require 'jiji/model/agents/agent'
require 'httpclient'
require 'json'

# ここで通貨ペアを複数指定。ただし、jiji上で同時にバックテストができるのは5通貨ペアまで。
TRADE_CURRENCIES = %i(USDJPY EURUSD USDJPY).freeze

# 通貨単位。最高で 通貨ペア×通貨単位 分保有することがあるので調整してください。
CURRENCY_UNIT = 10000

TENSORFLOW_API_URL = "http://tensorflow:5000/api/estimator".freeze

class TensorFlowAgent
  include Jiji::Model::Agents::Agent

  def self.description
    <<-STR
TensorFlowと連携してトレードするエージェントのサンプル
      STR
  end

  def self.property_infos
    [
      Property.new('exec_mode',
                   '動作モード("collect" or "trade" or "test")',
                   "collect")
    ]
  end

  def post_create
    @mode = create_mode(@exec_mode)
    @currencies = TRADE_CURRENCIES.map { |currency_pair| Currency.new(currency_pair, broker, @mode) }
  end

  # 次のレートを受け取る
  def next_tick(tick)
    timestamp = tick.timestamp
    return if already_check?(timestamp)
    @current_timestamp = timestamp
    logger.info "check for crossing at #{timestamp}"
    @currencies.each do |currency|
      currency.next_tick(tick)
      logger.info currency.do_trade
    end
  end

  # 1日に2回チェックを行う
  def already_check?(timestamp)
    return true if (timestamp.hour % 12).nonzero?
    !@current_timestamp.nil? && @current_timestamp.mday == timestamp.mday && @current_timestamp.hour == timestamp.hour
  end

  def create_mode(mode)
    case mode
    when 'trade' then
      TradeMode.new
    when 'collect' then
      CollectMode.new
    else
      TestMode.new
    end
  end

  # データ収集モード
  #
  # TensorFlowでの予測を使用せずに移動平均のシグナルのみでトレードを行い、
  # 結果をDBに保存する
  #
  class CollectMode
    def do_trade?(signal, sell_or_buy)
      true
    end

    # ポジションが閉じられたら、トレード結果とシグナルをDBに登録する
    def after_position_closed(signal, position)
      TradeAndSignals.create_from(signal, position).save
    end
  end

  # テストモード
  #
  # TensorFlowでの予測を使用せずに移動平均のシグナルのみでトレードする
  # トレード結果は収集しない
  #
  class TestMode
    def do_trade?(signal, sell_or_buy)
      true
    end

    def after_position_closed(signal, position)
      # do nothing.
    end
  end

  # 取引モード
  #
  # TensorFlowでの予測を使用してトレードする。
  # トレード結果は収集しない
  #
  class TradeMode
    def initialize
      @client = HTTPClient.new
    end

    # トレードを勝敗予測をtensorflowに問い合わせる
    def do_trade?(signal, sell_or_buy)
      body = { sell_or_buy: sell_or_buy }.merge(signal)
      body.delete(:ma5)
      body.delete(:ma10)

      result = @client.post(TENSORFLOW_API_URL, {
                              body: JSON.generate(body),
                              header: {
                                'Content-Type' => 'application/json'
                              }
                            })
      # up と予測された場合のみトレード
      JSON.parse(result.body)["result"] == "up"
    end

    def after_position_closed(signal, position)
      # do nothing.
    end
  end
end

# トレード結果とその時の各種指標。
# MongoDBに格納してTensorFlowの学習データにする
class TradeAndSignals
  include Mongoid::Document

  store_in collection: 'tensorflow_example_trade_and_signals'

  field :macd_difference,    type: Float # macd - macd_signal

  field :rsi,                type: Float

  field :slope_10,           type: Float # 10日移動平均線の傾き
  field :slope_25,           type: Float # 25日移動平均線の傾き
  field :slope_50,           type: Float # 50日移動平均線の傾き

  field :ma_10_estrangement, type: Float # 10日移動平均からの乖離率
  field :ma_25_estrangement, type: Float
  field :ma_50_estrangement, type: Float

  field :profit_or_loss,     type: Float
  field :sell_or_buy,        type: Symbol
  field :entered_at,         type: Time
  field :exited_at,          type: Time

  def self.create_from(signal_data, position)
    TradeAndSignals.new do |ts|
      signal_data.each do |pair|
        next if pair[0] == :ma5 || pair[0] == :ma10
        ts.send("#{pair[0]}=".to_sym, pair[1])
      end
      ts.profit_or_loss = position.profit_or_loss
      ts.sell_or_buy    = position.sell_or_buy
      ts.entered_at     = position.entered_at
      ts.exited_at      = position.exited_at
    end
  end
end

class Currency
  def initialize(currency_pair, broker, mode)
    @currency_pair = currency_pair
    @broker = broker
    @mode = mode
    @cross = Cross.new
  end

  def next_tick(tick)
    prepare_signals(tick) unless @macd
    @current_signals = calculate_signals(tick[@currency_pair])
    @cross.next_data(@current_signals[:ma5], @current_signals[:ma10])
  end

  def do_trade
    # 5日移動平均と10日移動平均のクロスでトレード
    if @cross.cross_up?
      log_text = buy
    elsif @cross.cross_down?
      log_text = sell
    end
    log_text || "#{@currency_pair} has no crossing"
  end

  def buy
    close_exist_positions
    return "#{@currency_pair} is cross up but tensorflow decided no-go" unless @mode.do_trade?(@current_signals, "buy")
    result = @broker.buy(@currency_pair, CURRENCY_UNIT)
    @current_position = @broker.positions[result.trade_opened.internal_id]
    @current_hold_signals = @current_signals
    "#{@currency_pair} is cross up and traded"
  end

  def sell
    close_exist_positions
    return "#{@currency_pair} is cross down but tensorflow decided no-go" unless @mode.do_trade?(@current_signals, "sell")
    result = @broker.sell(@currency_pair, CURRENCY_UNIT)
    @current_position = @broker.positions[result.trade_opened.internal_id]
    @current_hold_signals = @current_signals
    "#{@currency_pair} is cross down and traded"
  end

  def close_exist_positions
    return unless @current_position
    @current_position.close
    @mode.after_position_closed(@current_hold_signals, @current_position)
    @current_position = nil
    @current_hold_signals = nil
  end

  def calculate_signals(tick)
    price = tick.bid
    macd = @macd.next_data(price)
    ma10 = @ma10.next_data(price)
    ma25 = @ma25.next_data(price)
    ma50 = @ma50.next_data(price)
    {
      ma5:  @ma5.next_data(price),
      ma10: ma10,
      macd_difference: macd ? macd[:macd] - macd[:signal] : nil,
      rsi:  @rsi.next_data(price),
      slope_10: ma10 ? @ma10v.next_data(ma10) : nil,
      slope_25: ma25 ? @ma25v.next_data(ma25) : nil,
      slope_50: ma50 ? @ma50v.next_data(ma50) : nil,
      ma_10_estrangement: ma10 ? calculate_estrangement(price, ma10) : nil,
      ma_25_estrangement: ma25 ? calculate_estrangement(price, ma25) : nil,
      ma_50_estrangement: ma50 ? calculate_estrangement(price, ma50) : nil
    }
  end

  def prepare_signals(tick)
    create_signals
    retrieve_rates(tick.timestamp).each do |rate|
      calculate_signals(rate.close)
    end
  end

  def create_signals
    @macd  = Signals::MACD.new
    @ma5   = Signals::MovingAverage.new(5)
    @ma10  = Signals::MovingAverage.new(10)
    @ma25  = Signals::MovingAverage.new(25)
    @ma50  = Signals::MovingAverage.new(50)
    @ma5v  = Signals::Vector.new(5)
    @ma10v = Signals::Vector.new(10)
    @ma25v = Signals::Vector.new(25)
    @ma50v = Signals::Vector.new(50)
    @rsi   = Signals::RSI.new(9)
  end

  def retrieve_rates(time)
    @broker.retrieve_rates(@currency_pair, :one_day, time - 60 * 60 * 24 * 60, time)
  end

  def calculate_estrangement(price, ma)
    ((BigDecimal.new(price, 10) - ma) / ma * 100).to_f
  end
end

```

# Digital Ocean へのデプロイ手順

## Digital Ocean 上で Droplet を作成

サーバーを立ち上げる。Digital Ocean のデザインが全体的にシンプルで好き。
とりあえず公式だと CentOS で動作確認しているみたいなので、CentOS でサーバーの size は一番小さいやつにしておく。(運用中は基本的に 12 時間に 1 回しかアクション起こさないので、これで十分なはず)

<amp-img alt="DigitalOcean"
  src="/images/2016-10-26-jiji-tensorflow.png"
  width="1082"
  height="914"
  layout="responsive">
</amp-img>

## CentOS にアクセスして環境構築

先に、独自ドメインの DNS の設定と、Digital Ocean 側でもメニューの Networks から設定しておく。

また、今回は ssh キーを登録して root にログインしているので、`sudo` コマンドは使っていないので、適宜必要なところには入れてください。

Digital Ocean の管理画面に書かれている IP アドレスに `ssh root@{IPアドレス}` でアクセス

```sh

# 必要なパッケージをインストール
$ yum update -y
$ yum install -y epel-release
$ yum install -y docker git certbot # Let's Encrypt を利用するので certbot を入れる
$ certbot certonly --standalone -d {独自ドメイン} # 持っているドメインを設定して証明書を発行

# docker の設定
$ service docker start
$ chkconfig docker on

# docker-compose の構築
$ curl -L https://github.com/docker/compose/releases/download/1.8.1/docker-compose-Linux-x86_64 > /tmp/docker-compose #バージョンは最新の入れれば問題ない?
$ mv /tmp/docker-compose /usr/local/bin/
$ chmod +x /usr/local/bin/docker-compose

# jiji の構築
$ git clone https://github.com/unageanu/jiji-with-tensorflow-example.git
$ mkdir -p jiji-with-tensorflow-example/cert
$ mv /etc/letsencrypt/archive/{独自ドメイン}/cert1.pem jiji-with-tensorflow-example/cert/server.crt # Let's Encrypt で作成した証明書を移動
$ mv /etc/letsencrypt/archive/{独自ドメイン}/privkey1.pem jiji-with-tensorflow-example/cert/server.key
$ cd jiji-with-tensorflow-example
$ chown root.root cert/server.key
$ chmod 600 cert/server.key
$ vi docker-compose.yml # USER_SECRET を更新
$ vi build/tensorflow/Dockerfile # 3行目に `RUN pip install --upgrade pip` を挿入(最新版のpipでないとエラーになっていたので追加)
$ docker-compose up -d
```

## collect テストを実行

立ち上がったサーバー `https://{独自ドメイン}:10443` にアクセスし、jiji 上でエージェントを登録して collect mode のテストを実行する。

## 学習させる

```sh
# mongo DBに値が記録されているかを確認
$ docker exec -it jiji_example__mongodb  mongo
> use jiji
> db.tensorflow_example_trade_and_signals.find().count()
> quit()

# 学習させる
$ docker exec -it jiji_example__tensorflow /bin/bash

# docker 上での作業
$ cd /scripts/
$ python train.py

# たまに server.py が落ちてしまうことがあったので、forever.js を使ってずっと立ち上げ状態にしておく(docker の中をいじるのはあまり行儀が良くないが。。。)
$ apt update
$ apt install -y nodejs npm
$ npm install forever -g
$ ln -s /usr/bin/nodejs /usr/bin/node
$ forever start -c /usr/bin/python server.py
```

## trade テストを実行

今度は trade mode で実行して上手くいっているかを確認。

## 感想

今回やってみて、docker の知識とか色々ついた。こういう触れるオモチャ的なものがあると学習って進む感じ。
jiji にはかなり感謝してます。
