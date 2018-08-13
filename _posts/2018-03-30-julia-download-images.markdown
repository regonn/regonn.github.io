---
layout: post
title: "Julia でJSONに記述されている画像ファイルをダウンロードする"
date: 2018-03-30 9:41:28 +0900
categories: julia
---

[【3/30 開催】ITOC 機械学習もくもく会 #08 - connpass](https://itoc.connpass.com/event/81181/)  での作業内容

Kaggle の[iMaterialist Challenge](https://www.kaggle.com/c/imaterialist-challenge-furniture-2018)問題で Julia を使って画像ファイルをダウンロードする。

また、画像ファイルは 404 や 403 が発生して取得できない場合があるため、エラーハンドリングもしておく。

とりあえず先頭の 10 個の画像を images フォルダに `{model_id}.jpg`  の形で保存していくコードを書く。

<amp-gist
  data-gistid="778f23144fd7d5914fafa3743d80c96b"
  layout="fixed-height"
  height="225">
</amp-gist>

jpg 以外は考慮していない。

今後は画像のサイズ処理等フィルター関連をしていきたい。
