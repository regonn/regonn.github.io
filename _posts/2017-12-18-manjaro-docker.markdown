---
layout: post
title: "Manjaro Linux で docker.service が起動しない場合の対処方"
date: 2017-12-18 9:41:28 +0900
categories: Manjaro
---

Manjaro Linux で docker.service を systemctl で起動しようとしてもエラーになっていたので対処方のメモ

## エラー時の systemctl status docker.service 結果

```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Mon 2017-12-18 10:19:40 JST; 5min ago
     Docs: https://docs.docker.com
  Process: 2193 ExecStart=/usr/bin/dockerd -H fd:// (code=exited, status=1/FAILURE)
 Main PID: 2193 (code=exited, status=1/FAILURE)

12月 18 10:19:40 xxxxxx-pc systemd[1]: docker.service: Service hold-off time over, scheduling restart.
12月 18 10:19:40 xxxxxx-pc systemd[1]: docker.service: Scheduled restart job, restart counter is at 3.
12月 18 10:19:40 xxxxxx-pc systemd[1]: Stopped Docker Application Container Engine.
12月 18 10:19:40 xxxxxx-pc systemd[1]: docker.service: Start request repeated too quickly.
12月 18 10:19:40 xxxxxx-pc systemd[1]: docker.service: Failed with result 'exit-code'.
12月 18 10:19:40 xxxxxx-pc systemd[1]: Failed to start Docker Application Container Engine.
```

## 解決策
次のファイルを作って以下の設定をする

```/etc/systemd/system/docker.service.d/override.conf
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H fd:// -s overlay2
```

新しい設定を読み込ませるために次のコマンドを実行

```
$ systemctl daemon-reload
$ systemctl restart docker.service
```
