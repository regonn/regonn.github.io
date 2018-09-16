---
layout: post
title: "VR空間上でIT系勉強会を開催しました(cluster編)"
date: 2018-09-16 17:00:00 +0900
categories: VR
youtube: "bDSbfbxtLm0"
eyecatch_file: "workshop_in_vr.png"
desc: "VR空間上でIT系勉強会をやってみて、やった内容とclusterというサービスに未来を感じたので書いていきます。"
keywords: "VR IT勉強会 WorkshopInVR コミュニティ れごん YouTubeLive"
---

## 発表資料スライド

- [Workshop in VR \#1 発表資料 \- Speaker Deck](https://speakerdeck.com/regonn/workshop-in-vr-number-1-fa-biao-zi-liao)
- [Kaggle の Julia Kernel が動く Docker を 1\.0 に対応させる \- Speaker Deck](https://speakerdeck.com/regonn/kaggle-false-julia-kernel-gadong-ku-docker-wo-1-dot-0-nidui-ying-saseru)

## なぜ VR 上で勉強会を開催しようと思ったか

- 島根県松江市に住んでいて、Ruby 界隈の勉強会はあるけど、機械学習系とか少ない
- 開催しても人が集まらないことも多い(人望・宣伝不足もある)
- 最近 VR 技術にハマっている
- 自分の所属しているコミュニティが Slack や Discord 上で形成されているのが多いと感じて同じように勉強会コミュニティも作ってみようと思った

## VR 上で行うことで解決する問題

スライドより
<amp-img src="/images/2018-09-16-workshop-in-vr1.png" alt="VR勉強会で解決する問題" width="960px" height="540px" layout="responsive" ></amp-img>

## cluster

- [cluster（クラスター）｜バーチャルイベント空間](https://cluster.mu/)
- VR 上でイベントを開催できるサービス
- [VTuber・輝夜月の VR ライブ](https://www.moguravr.com/vtuber-luna-live-rep1/)を行ったりとエンタメ系でも強い

## cluster を使ってみて

### 良かった所

- VR 機器を持っていない人でも参加可能
- VRM データ(3D モデル)があれば自分のアバターに設定できる
- PDF のプレゼン表示、コメントフィード表示、拍手モーションなど勉強会に必要なものは揃っている
- UI がしっかりしていて、未来を感じる
- いくらでもルームが作成できるので、同じ環境で発表準備ができる
- くらすたーちゃん可愛い

### 悪かった所

- マイク環境が人によって違うため発表音量がバラバラになる
- 設定した YouTubeLive ページのコメントしか連携されないので配信環境を別途準備する必要が出てくる
- たまにスライドボタンの次へを押しても反応がなかったりする
- VR でやろうとするとメニューのボタンが左手首に付いていることに気づかない(n=2)

<amp-img src="/images/2018-09-16-workshop-in-vr3.jpg" alt="cluster" width="1026px" height="798px" layout="responsive" ></amp-img>
自分でウィンドウ位置を空間上に自由に配置できるのは VR ならでは

## cluster での YouTubeLive 配信方法

- cluster 発表 PC
  - cluster のメインアカウント
  - 発表等はこちらで行う
  - VR も着用
- 配信 PC
  - cluster のサブアカウント
  - カメラ係
  - OBS 等配信ソフトで cluster ウィンドウをキャプチャして YouTube へ

## 勉強会としての改善点

- connpass での視聴者管理
  - connpass では発表者枠しか管理しなかった
  - cluster での視聴者も管理できたほうが、人数の把握や視聴者も connpass のリマインダーやカレンダー機能が使える
  - 次回から視聴者枠も connpass に追加しておく
- YouTube の配信の不安定
  - OBS のデフォルト設定で臨んでいたため、リアルタイム配信には重かった可能性がある
  - また配信用 PC は Wifi だったため、上り回線が安定していいなかった
  - OBS の配信設定値を見直し、イーサネットケーブルに繋ぐ
- マイク音量問題
  - マイクを有効にすると音量ゲージが表示されるので、発表者には先にここらへんに Max がなるようにと連絡を入れておく
- 発表者が発表中に落ちる
  - VR 上なのでしかたないが、発表者の通信環境によっては回線落ちが発生する
  - 発表者側の環境は関われないので、司会者は発表社が落ちたと思ったらすぐにワンマンショーを始める気持ちでいる

## 勉強会情報

### 月 1 開催で次回開催は 10 月 7 日(日)

[Workshop in VR \#2 LT 会 \- connpass](https://workshop-in-vr.connpass.com/event/101802/)

### Workshop in VR(connpass)

[Workshop in VR \- connpass](https://workshop-in-vr.connpass.com/)

### Slack コミュニティ

[招待用リンク](https://join.slack.com/t/workshop-in-vr/shared_invite/enQtNDIzMTY0OTE4OTEyLTdjZDVjN2MyN2ZjNjIwMTU4NmE5M2Y0MzRjYTg0NmQxMTkyZGVjMDk3OTcxZGJlZTZhMDBkNzY5NGE5ZDQ4NDE)

## ひとりごと

VR 空間で自撮り楽しい
<amp-img src="/images/2018-09-16-workshop-in-vr2.png" alt="自撮り" width="1426px" height="922px" layout="responsive" ></amp-img>
