---
layout: post
title: "Yattecastをamp対応させてPodcastを配信していく"
date: 2018-12-12 22:30:00 +0900
categories: "podcast"
eyecatch_file: ""
desc: "Podcastサイトを作るためのテンプレートYattecastをamp対応させたのでその話"
keywords: "Podcast,Yattecast,amp"
---

[Podcaster Advent Calendar 2018](https://adventar.org/calendars/3068) の 12 日の記事です。
カレンダーの趣旨勘違いしてて、技術的なことをやって記事書いてしまいましたが、普段やっている Podcast も本日 2 つ通常回ですが公開します。

## 本日公開 Podcast

- [12. オフ会後 - regonn&curry.fm](https://regonn-curry-fm.github.io/episode/12)
  - データサイエンス Podcast Twitter: [@regonn_curry](https://twitter.com/regonn_curry)
- [9. ふたりは藤木くん - 今夜も strong x strong](https://strong-strong.github.io/episode/9)
  - 底辺 Podcast、ストロングゼロをただ飲んでるだけ Twitter: [@strongxstrong_2](https://twitter.com/strongxstrong_2)

## Yattecast

[Yattecast - Podcast サイトをつくるためのテンプレート](https://r7kamura.github.io/yattecast/)

今年は新しく２つの Podcast を始めたんですが、始めるきっかけや続けるモチベーションを保てたのは、このテンプレートを使って Github pages により無料で公開できたのが大きいです。

始めた時の技術的な内容は次の記事にまとめてあります。

[Tech 系 Podcast の始め方](https://blog.regonn.tokyo/podcast/2018/10/07/podcast.html)

## amp 対応

Yattecast があまりメンテナンスされていないのと、あまりアニメーション等もないので、これは amp 対応したほうがメリットありそうと思って今回挑戦してみました。

## 成果

- [regonn/regonn-cast](https://github.com/regonn/regonn-cast)
- [差分(コミット単位でみてもらうと何をやったかわかりやすいと思います)](https://github.com/r7kamura/yattecast/compare/master...regonn:master)

## 対応したこと

- Gemfile 更新
  - [Dependabot](https://dependabot.com/)を使って今後もライブラリのアップデートに対応して更新していけるようにしました
- Google Podcast 対応
  - ちゃんと対応しているのかは [Podcast Publisher Tools](https://search.google.com/devtools/podcast/preview?hl=ja) で確認できるっぽい
- amp 対応
  - [amp-audio](https://www.ampproject.org/docs/reference/components/amp-audio) でポッドキャストも再生できるように
  - [amp-analytics](https://www.ampproject.org/docs/reference/components/amp-analytics)
  - [amp-auto-ads(google adsense の自動広告)](https://www.ampproject.org/docs/reference/components/amp-auto-ads)
  - [amp-social-share](https://www.ampproject.org/docs/reference/components/amp-social-share)
- Schema.org の構造化対応
- 削除した部分
  - Pushdog がもうサービスをやっていないので削除
  - mediaelement-and-player
  - jQuery

これで、Podcast を始める人が増えてくれると嬉しいです。
