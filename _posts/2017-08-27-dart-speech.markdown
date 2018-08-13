---
layout: post
title: "YouTubeLiveのコメントをWebSpeechAPIで読み上げてくれるツールをDartで書いてみた"
date: 2017-08-27 9:41:28 +0900
categories: dart
youtube: 'lk_Tlv0AmxU'
---

# モチベーション
YouTubeでプログラミングのライブ配信するときに、ニコニコ生放送ではお馴染みの「棒読みちゃん」が欲しかった。
けど、Mac環境だと使い勝手が悪いのでYouTubeのAPIとWebSpeechAPI(音声合成)でWebブラウザで動く棒読みちゃんを自分で作ってみることに。

趣味でDartを勉強しているのとYouTubeはgoogleだし相性もいいかなと思ってDart実装にチャレンジしてみた。

名前は、棒読みちゃんよりWebSpeechAPIの音声の人の方がしっかりした声なので、棒読みさんと名付ける。

[regonn/bouyomi-san-youtube-live.dart](https://github.com/regonn/bouyomi-san-youtube-live.dart)

ローカルにDart環境が揃っていれば、Dart project scaffolding generatorの[stagehand](https://github.com/google/stagehand)を使って `web-simple` モードで生成すればすぐに開発が始められる。

# コード
### main.dart
``` dart
import 'dart:html';
import 'dart:async';
import 'package:googleapis_auth/auth_browser.dart' as auth;
import 'package:googleapis/youtube/v3.dart' as youtube;

DateTime lastMessagedAt = new DateTime.now();

void main() {
  InputElement apiKeyInput = querySelector('#api-key');
  InputElement channelIdInput = querySelector('#channel-id');
  ButtonElement setButton = querySelector('#set-button');

  displayOutput('APIキーとチャンネルを設定してください。');
  speak('起動しました。エーピーアイキーとチャンネルを設定してください。');

  setButton.onClick.listen((_) {
    var apiKey = apiKeyInput.value;
    var client = auth.clientViaApiKey(apiKey);
    var api = new youtube.YoutubeApi(client);
    api.search.list("id", channelId: channelIdInput.value, type: 'video', eventType: 'live')
      .then((youtube.SearchListResponse response) {
        var liveVideoId = null;
        if (response.items != null) {
          liveVideoId = response.items.first.id.videoId;
          api.videos.list("liveStreamingDetails,snippet", id: liveVideoId).then((youtube.VideoListResponse videoResponse) {
            var liveChatId = null;
            if (videoResponse.items != null) {
              var video = videoResponse.items.first;
              liveChatId = video.liveStreamingDetails.activeLiveChatId;
              var title = video.snippet.title;
              speak('$title のチャンネルに設定されました');
              displayOutput(title);
              lastMessagedAt = new DateTime.now();
              const duration = const Duration(seconds:5);
              new Timer.periodic(duration, (Timer t) => speakNewMessages(api, liveChatId));
            }
          });
        }
      })
      .catchError((e) {
        const errorText = 'ライブ情報の取得に失敗しました。入力情報が正しくない可能性または、ライブ放送が行われていない可能性があります。';
        speak(errorText);
        displayOutput(errorText);
      });
  });
}

void speakNewMessages(youtube.YoutubeApi api, String liveChatId) {
  api.liveChatMessages.list(liveChatId, 'snippet').then((youtube.LiveChatMessageListResponse messagesResponse) {
    if (messagesResponse.items != null) {
      var speakMessages = messagesResponse.items.where((item)=> item.snippet.publishedAt.isAfter(lastMessagedAt)).toList();
      for(youtube.LiveChatMessage message in speakMessages ){
        lastMessagedAt = message.snippet.publishedAt;
        speak(message.snippet.displayMessage);
      }
    }
  });
}

void speak(String text) {
  var u = new SpeechSynthesisUtterance();
  u.text = text;
  u.lang = 'ja-JP';
  u.rate = 1.4;
  window.speechSynthesis.speak(u);
}

void displayOutput(String text) {
  querySelector('#output').text = text;
}
```

LiveChatIdを取得するために、2回apiを叩いているためネストしてしまって読みにくいが、特に難しいことはしていない。

APIの仕様上、最低のコメント取得が200個なので、過去のものを読み込まないように `lastMessagedAt` を変数で持っておいて、ボタンを押して開始したタイミングだったり、読み上げたメッセージの時間で更新をかけて、同じものは読み上げないようにしている。

# Dartを触ってみて
## 良いところ
一度はJavascriptの置き換えを狙った言語でもあって、とてもHTMLとの相性が良い。

WebSpeechAPIで読み上げる部分も

``` dart
void speak(String text) {
  var u = new SpeechSynthesisUtterance();
  u.text = text;
  u.lang = 'ja-JP';
  u.rate = 1.4;
  window.speechSynthesis.speak(u);
}
```

のように簡単に記述できるし、クリックの管理も

``` dart
ButtonElement setButton = querySelector('#set-button');
setButton.onClick.listen((_) {})
```

みたいに、jQueryを書かないで実装できてしまうので良かった。

外部のパッケージもAPI取得で使う二つしか使わなかった。

``` dart
import 'package:googleapis_auth/auth_browser.dart' as auth;
import 'package:googleapis/youtube/v3.dart' as youtube;
```

## 悪いところ
ご存知の通り、Dart言語は一度は盛んになりかけて現在は下火なので、最新のコードサンプルが少なかったりライブラリが古いものが多い。

Google公式のものは比較的新しいが、[googleapi のサンプルコード](https://github.com/dart-lang/googleapis_examples)も三年前とかで、本当に動くのか不安になった。

## デモサイト
[コチラ](https://bouyomisan.regonn.tokyo/)で実際に動きを確認できます。(実際に動かすにはYouTubeDataAPI v3が有効になっているAPIキーが必要です。)
