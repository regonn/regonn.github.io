---
layout: post
title: "グラフ・ネットワーク分析のJSON結果をブラウザ上で描画する Cytoscape.js"
date: 2018-05-21 20:00:00 +0900
categories: cytoscape
eyecatch_file: 'cytoscape.png'
---

[【5/21開催】ITOC機械学習もくもく会 \#10 \- connpass](https://itoc.connpass.com/event/87455/) での作業内容。

最近グラフ・ネットワーク分析(Facebookの友達から相関図を出す等)を勉強していて、解析結果を画像ではなくjsでブラウザ上に表示してみたかったので、グラフ・ネットワークをJSONから描画するjsライブラリを調べてみました。

もうちょっと、ネットワーク分析を知りたい人は下の記事とか面白いです。

[HIP HOPでわかるネットワーク分析 \- Aidemy Tech Blog](http://blog.aidemy.net/entry/2018/02/23/135441)

## jsのネットワーク・グラフ描画ライブラリ
検索してみたらいくつか発見

- [vis\.js \- A dynamic, browser based visualization library\.](http://visjs.org/)
- [Cytoscape\.js](http://js.cytoscape.org/)
- [JSNetworkX](http://jsnetworkx.org/)
- [Sigma js](http://sigmajs.org/)
- [arbor\.js](http://arborjs.org/)

js界隈あるあるですが、殆どがメンテナンスがされていない状態。。。

[Cytoscape\.js](http://js.cytoscape.org/) だけは、現在もメンテナンスされているみたいなので、これで触ってみることに。

## [Cytoscape\.js](http://js.cytoscape.org/)
何ができるかは、TOPページのDemosにあるのと、コードもみることができます。

あとは、他の人の記事でSlackの内容からコミュニケーションをCytoscape.jsで可視化している例もありました。

[cytoscape\.jsとslack APIでチーム内の関係を可視化](https://intheweb.io/cytoscape-jstoslack-apidetimunei-noguan-xi-woke-shi-hua/)


## コード
もくもく会で時間も限られていたので基本的な部分だけ触ってみました。
Codepenでhtml,css,jsで書いて触れるようになってます。

[Cytoscape - Codepen](https://codepen.io/regonn/pen/WJPgrW/)

jsのコードはこんな感じ

``` js
var cy = cytoscape({
  container: document.getElementById('cy'),

  style: cytoscape.stylesheet()
    .selector('node')
      .css({
        'height': 'data(size)',
        'width': 'data(size)',
        'border-color': '#000',
        'border-width': '1',
        'content': 'data(name)'
      })
    .selector('edge')
      .css({
        'width': 'data(strength)'
      })
    .selector('#1')
      .css({
        'background-color': 'red'
      }),

  elements: {
    nodes: [
      { data: { id: '1', size: 50, name: 'a' } },
      { data: { id: '2', size: 20, name: 'b' } },
      { data: { id: '3', size: 40, name: 'c' } }
    ],
    edges: [
      { data: { source: '1', target: '2', strength: 5 } },
      { data: { source: '1', target: '3', strength: 20 } }
    ]
  },
});
```

## 触ってみての感想
- jsの中でCSSを定義するから、ReactとかElmみたいで今風？
- elementsにノード(節点・頂点)とエッジ(枝・辺)の情報を書いていくので、ここの部分をJSONで読み込んで上げればデータを表示できそう
- エッジの太さとかをCSSで書かないといけないのは、少し面倒に感じた
- ただ、CSS定義のため自由に色々とできる(ノードを画像にしたりも簡単にできる)

みなさんもネットワーク分析結果をサイトに載せたい時とかは使ってみてください。