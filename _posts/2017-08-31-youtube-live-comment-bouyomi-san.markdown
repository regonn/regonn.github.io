---
layout: post
title: "【Mac,Linux向け】 YouTubeLiveのコメントを棒読みちゃん風に読み上げてくれるWEBアプリを作ってみた"
date: 2017-08-31 00:00:00
categories: 'programmer'
desc: "YouTubeLiveのコメントを棒読みちゃんっぽく読み上げたかったので、ブラウザベースで動くようにして、MacやLinuxでも使えるようにしてみました。"
keywords: "YouTubeLive,棒読みちゃん,Mac,Linux"
image: "https://blog.regonn.tokyo/images/2017-08-31-youtube-live-comment-bouyomi-san.png"
---
<amp-img src="/images/2017-08-31-youtube-live-comment-bouyomi-san.png" alt="部屋" width="337px" height="400px" layout="responsive" ></amp-img>

## 作った経緯
YouTubeLiveで配信することがたまにあり、コメントを読み上げてくれる[棒読みちゃん](http://chi.usamimi.info/Program/Application/BouyomiChan/)みたいなソフトを探してた。

しかし、基本Windowsアプリだったり、htmlで動くのもあったけど、結局Windows環境でしか動かないようだったため、自分でMacやLinux環境でも使えるようにブラウザで実装してみた。

WebSpeachAPIというブラウザの機能で読み上げるようにしてあるので、最新のWEBブラウザだったら動くはず。

## アプリ
[コチラ](https://bouyomisan.regonn.tokyo/)で公開している。

※アクセスして、ブラウザが対応していると最初に音が出るので注意。


## 設定方法

<amp-youtube
  data-videoid="u6cDhn8qw38"
  layout="responsive"
  width="480"
  height="270">
</amp-youtube>
