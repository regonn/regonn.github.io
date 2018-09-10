---
layout: post
title: "VTuberハッカソン 全国ツアー2018【島根会場】で最優秀チームに選ばれたので、やったことを書いていく"
date: 2018-09-09 9:00:00 +0900
categories: VR
youtube: "tSViZtkQVaM"
eyecatch_file: "regonn_ch_title.png"
desc: "VTuberハッカソンで取り組んだ作業内容をメモがてら書いていく。"
keywords: "VR UE4 モクリ VRアカデミア OculusRift 俳句 VTuberハッカソン"
---

## 発表資料スライド

[https://speakerdeck.com/regonn/vtuberhatukason-quan-guo-tua2018-dao-gen-hui-chang-timu-freeee](https://speakerdeck.com/regonn/vtuberhatukason-quan-guo-tua2018-dao-gen-hui-chang-timu-freeee)

## この記事で書くこと

2018/09/09 開催の[VTuber ハッカソン 全国ツアー 2018【島根会場】 \- connpass](https://chiikiokoshi-vr.connpass.com/event/86808/) でやった作業内容

## 使った技術

- [Unreal Engine 4](https://www.unrealengine.com/ja/what-is-unreal-engine-4)
  - チームが 2 人のため、なるべく少ないコストで綺麗なシーンを撮りたかった
- [Substance Painter \- 3D Painting Software](https://www.allegorithmic.com/products/substance-painter)
  - オブジェクトの編集などで利用
- [Oculus Rift \| Oculus](https://www.oculus.com/rift/#oui-csl-rift-games=mages-tale)
  - Vive が提供されていたけど、二人とも Oculus Rift を持っていたので、操作も慣れており採用
- [レッサーモクリ](http://mokuri.world/%e3%83%ac%e3%83%83%e3%82%b5%e3%83%bc%e3%83%a2%e3%82%af%e3%83%aa-%e3%83%80%e3%82%a6%e3%83%b3%e3%83%ad%e3%83%bc%e3%83%89/)
  - [VR 法人 HIKKY](https://www.hikky.life/) が[モクリプロジェクト](http://mokuri.world/%e3%83%a2%e3%82%af%e3%83%aa%e3%83%97%e3%83%ad%e3%82%b8%e3%82%a7%e3%82%af%e3%83%88/)で公開している無料で営利目的で使って大丈夫なライセンス
  - 2 チームだったため出来合いのものを使いたかった
  - 私がケモナー
- [Open Broadcaster Software](https://obsproject.com/ja)
  - 録画

### モクリを Unreal Engine に取り込む

公開されているモクリデータは unity project 形式だったので次の様な方法で Unreal Engine に取り込みました。(方法探り探りやったので、もっと効率の良い方法はありそう)

- モクリ unity project の assets から fbx ファイルを取り出す
- Substance Painter で着色、Unreal Engine 用ファイルでエクスポートする
- Unreal Engine で fbx を取り込み Substance Painter で着色した png を入れる

### Unreal Engine で VR モーションを連携する

いくつかの記事を参考にして Oculus Rift のモーションを取り込む

- [【チュートリアル】Gray ちゃんになれる！　 UE4×Oculus でカンタンアバター \| 特集 \| CGWORLD\.jp](https://cgworld.jp/feature/201804-cgw237t1-gray.html)
- [UE4 で VRIK みたいなことしたい](https://qiita.com/broken55/items/3fb800ef5520e81326e8)
- 腕と頭が連動して動くところまで持っていく

### 録画する

- Unreal Engine の VR プレビューで再生
- Spectator Screen 機能を使ってカメラを用意してそれをプレビューで表示する(YouTube 動画用に 1920x1080px に設定)
- 録画内容は[【UE4】Scene Capture 2D で別視点から見たものを写す](https://tubezgames.com/2017/09/ue4-scenecapture2d/) を参考に鏡を用意してリアルタイムで確認できるように
- OBS を使って音声は Oculus から取り込み、Unreal Engine のプレビューを Window キャプチャ

## 所感

- Unreal Engine の日本語記事少なくて苦労する
- 記事読んでも専門用語ばっかりで何をしたらいいのか分からない
- ノードでのプログラミング楽しい
- VR モーションキャプチャは、ある程度 Unreal Engine 側で実装してくれているので楽だった
