---
layout: post
title: QNAP の Docker
date: 2021-09-12 17:30 +0900
---
QNAP の Docker を使ってみる。


## はじめに

QNAP のデータベースを使っていると、アプリケーションサーバも設置したくなってくる。


## QNAP に Container Station をインストールする

QNAP の App Center から Container Station をインストールすると、
Docker が使用できるようになる。

Container Station を起動するとこんな感じ。

![Container Station](/images/2021-09-13-qnap-container-station.png)


初回時には、
次のような LXC サポート終了通知 が表示されるが、
Docker を使用する際には関係ないので
「今後、このメッセージを表示しない」
にチェックを入れて OK して構わない。

![LXCサポート終了通知](/images/2021-09-13-qnap-container-station-dialog-lxc.png)


### Container Station でのアプリケーケーション作成

Container Station サイドメニューの
管理-作成
で Docker イメージが用意されているのが確認できる。

![イメージの作成](/images/2021-09-13-qnap-container-station-create.png)


Ubuntu:bionic の欄の「インストール」ボタンで
Docker イメージの作成からコンテナの実行までが行われる。

サイドメニューのコンテナに切り替えて
名称のところのリンクをクリックすると
コンソールが表示される。
Enter キーを押す必要がある。

### Docker Hub のイメージの使用

サイドバーメニューの「イメージ」で
ウィンドウ右上の「プル」ボタンをクリックすると、
下のようなダイアログが表示され、
Docker Hub などの外部レジストリの Docker イメージを利用することができる。

![Dockerイメージのプル]({{site.baseurl}}/images/2021-09-12-qnap-docker-pull-image.png)


### リモートから QNAP Docker の操作

PC から QNAP (Container Station) 上の Docker に接続

![Docker証明書]({{site.baseurl}}/images/2021-09-12-qnap-docker-cert.png)

QNAP からダウンロードした cert.zip を
%HOMEPATH%\.docker に展開する。
cert.zip には ca.pem, cert.pem, key.pem が含まれている。

`docker -H=tcp://(QNAPのIPアドレス):2376' --tlsverify ps`
のようにしてリモート (QNAP) の docker を操作することができる。


接続先 `tcp://192.168.0.128:2376`

```
PS D:\test> docker -H='tcp://192.168.0.128:2376' --tlsverify ps
CONTAINER ID   IMAGE                          COMMAND       CREATED             STATUS             PORTS     NAMES
340403fce20e   arm32v7/ubuntu:bionic-data-1   "bash"        3 hours ago         Up 2 hours                   ubuntu-1
```

コマンドラインオプション `-H`, `--tlsverify` の代わりに
環境変数 `DOCKER_HOST`, `DOCKER_TLS_VERIFY` を使用することもできる。

https://docs.docker.com/engine/security/protect-access/


### VSCode からの操作

settings.json で、
`docker.host`, `docker.certPath`, `docker.tlsVerify`
を指定する。

```jsonc
{
    // 略
    "docker.host": "tcp://192.168.0.128:2376",
    "docker.certPath": "C:\\Users\\chirimen\\.docker",
    "docker.tlsVerify": "1",
    // 略
}
```

ca.pem の内容は次のようになっています。

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            93:12:84:b8:b7:9b:97:3f:8d:20:cd:42:41:e1:82:d6
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: O = QNAP
        Validity
            Not Before: Sep 11 13:11:00 2021 GMT
            Not After : Aug 26 13:11:00 2024 GMT
        Subject: O = QNAP
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
(略)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Key Agreement, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier:
                47:17:6A:A7:62:98:E6:6B:27:5E:AC:51:4A:68:16:30:06:98:6D:02
    Signature Algorithm: sha256WithRSAEncryption
(略)
```

cert.pem は

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            13:da:72:d8:13:e9:d2:2a:d5:44:de:19:ae:9c:c2:c3
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: O = QNAP
        Validity
            Not Before: Sep 11 13:11:00 2021 GMT
            Not After : Aug 26 13:11:00 2024 GMT
        Subject: O = unknown, CN = client
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
(略)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Key Agreement
            X509v3 Extended Key Usage:
                TLS Web Client Authentication, TLS Web Server Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier:
                keyid:47:17:6A:A7:62:98:E6:6B:27:5E:AC:51:4A:68:16:30:06:98:6D:02

    Signature Algorithm: sha256WithRSAEncryption
(略)
```
