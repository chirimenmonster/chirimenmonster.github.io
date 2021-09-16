---
layout: post
title: QNAP の Docker を使ってみる
date: 2021-09-16 21:20 +0900
image: /images/2021-09-14-qnap-container-console-ubuntu.png
---
QNAP で Docker が使用できるようなので使ってみた。
Docker Hub のイメージも利用できるし、
試してはいないが docker compose も使えるようなので、
思ったより柔軟性の高い運用ができそうだ。

## はじめに

古い NetBSD サーバからのデータの移行先として QNAP を設置し、
ファイルサーバ、データベースを移動した。
こうなってくると、常時稼働のサーバとして QNAP を運用したくなってくる。
過度に QNAP に特化した構成にするつもりはなかったのだが、
Docker 上で運用できるなら、ということで、
QNAP での Docker の使用感を試してみた。

## QNAP で Docker を使用する準備

### Container Station をインストールする

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


## QNAP で Docker イメージを使用する
## Container Station に用意されている Docker イメージを使用する

Container Station サイドメニューの
管理-作成
で Docker イメージが用意されているのが確認できる。

![イメージの作成](/images/2021-09-13-qnap-container-station-create.png)

各 Docker イメージの欄にある「インストール」ボタンをクリックすると、
Docker イメージをダウンロードしてコンテナを作成することができる。

Ubuntu:bionic の「インストール」ボタンをクリックすると、
図のようにコンテナのパラメータ設定ダイアログが表示される。

![コンテナの作成](/images/2021-09-14-qnap-container-create-ubuntu.png)

デフォルトでは「自動実行」が設定されており、
「作成」をクリックするとコンテナの実行まで行われる。

コンテナはサイドメニューの「概要」または「コンテナ」で確認できる。

![Dockerコンテナの確認](/images/2021-09-14-qnap-container-running-ubuntu.png)

コンテナの名称のところのリンクをクリックすると
コンソールが表示される。
何も表示されていない場合は、一度 Enter キーを押すと表示される。

![Dockerコンテナのコンソール表示](/images/2021-09-14-qnap-container-console-ubuntu.png)

Container Station の一覧に出てきている Ubuntu は
Bionic 18.04
で、やや古いが、後述するように Docker Hub のイメージを使用することもできるので問題はない。

## Docker Hub のイメージを使用する

サイドバーメニューの「イメージ」で
ウィンドウ右上の「プル」ボタンをクリックすると、
下のようなダイアログが表示され、
Docker Hub などの外部レジストリの Docker イメージを利用することができる。

![Dockerイメージのプル]({{site.baseurl}}/images/2021-09-12-qnap-docker-pull-image.png)


## リモートから QNAP の Docker を操作する

PC 上の Docker を使用して QNAP (Container Station) 上の Docker に接続し、操作することができる。
QNAP の Docker に接続する際は TLS クライアント認証が用いられ、
その証明書は Container Station からダウンロードしたものを使用する。

### Container Station から Docker 証明書をダウンロードする

Docker 証明書は、リモートの (QNAP 上の) Docker との接続に用いる TLS 証明書である。
Container Station でルート証明書、クライアント証明書、秘密鍵のセットを生成し、ダウンロードする。


Container Station サイドメニュー
「管理」-「初期設定」
のタブ
「Docker証明書」
から証明書を「ダウンロード」する。

![Docker証明書]({{site.baseurl}}/images/2021-09-12-qnap-docker-cert.png)

### コマンドラインオプションまたは環境変数でサーバを指定する

#### 証明書を Docker 規定のフォルダに設置する 

リモートの Docker サーバをコマンドラインオプション、または環境変数で指定する場合
(dokcer context を使用しない場合)
には、
QNAP からダウンロードした `cert.zip` をユーザーのホームパス下のフォルダ `.docker`
すなわち `%HOMEPATH%\.docker` に展開する。
`cert.zip` には `ca.pem`, `cert.pem`, `key.pem` が含まれている。

#### コマンドラインオプションで接続先サーバを指定して docker を実行する


`docker -H=tcp://(QNAPのIPアドレス):2376' --tlsverify ps`
のようにしてリモート (QNAP) の docker を操作することができる。


接続先 `tcp://192.168.0.128:2376`

```
PS D:\test> docker -H='tcp://192.168.0.128:2376' --tlsverify ps
CONTAINER ID   IMAGE                          COMMAND       CREATED             STATUS             PORTS     NAMES
340403fce20e   arm32v7/ubuntu:bionic-data-1   "bash"        3 hours ago         Up 2 hours                   ubuntu-1
```

#### 環境変数で接続先サーバを指定して docker を実行する

コマンドラインオプション `-H`, `--tlsverify` の代わりに、
環境変数 `DOCKER_HOST`, `DOCKER_TLS_VERIFY` を使用することもできる。

一つのサーバしか指定できないので、
ローカルの docker を使用したり、複数のサーバを使用する場合には向かない。


```
PS D:\test> $env:DOCKER_HOST='tcp://192.168.0.128:2376'
PS D:\test> $env:DOCKER_TLS_VERIFY=1
PS D:\test> docker ps
CONTAINER ID   IMAGE                          COMMAND       CREATED             STATUS             PORTS     NAMES
340403fce20e   arm32v7/ubuntu:bionic-data-1   "bash"        3 hours ago         Up 2 hours                   ubuntu-1
```


### Docker context で QNAP の Docker を指定する

接続先サーバを切り替える必要がある場合には docker context が利用できる。
dockder context は操作対象の Docker サーバへの接続情報をあらかじめ登録しておき、
必要に応じて切り替えて接続する仕組みである。

`docker context ls` で現在設定されているコンテキストの一覧を確認できる。
下の例では、`default` と `destktop-linux` の2つのコンテキストがすでに登録されていて、
コンテキスト名の後ろに `*` が表示されている `default` コンテキストが現在選択されていることがわかる。

```
PS D:\test> docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                             KUBERNETES ENDPOINT   ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   npipe:////./pipe/docker_engine                                    swarm
desktop-linux       moby                                                          npipe:////./pipe/dockerDesktopLinuxEngine
```

docker context では、証明書は各コンテキストの接続情報とともに保存されるので、
まず、QNAP からダウンロードした cert.zip を適当なフォルダ
(この例ではカレントフォルダに `tmp` フォルダを作成した)
に展開する。

```
PS D:\text> unzip -d tmp cert.zip
Archive:  cert.zip
 extracting: tmp/ca.pem
 extracting: tmp/cert.pem
 extracting: tmp/key.pem
```

`docker context create` で適当な名前でコンテキストを作成する。
ここでは `qnap` を指定している。
オプション `--docker` の引数で接続のためのパラメータを指定する。
ここで必要なパラメータは `host`, `skip-tls-verify`, `ca`, `cert`, `key` である。
存在しないパラメータを指定するとエラーになる。

```
PS D:\test> docker context create qnap --docker "host=tcp://192.168.0.128:2376,skip-tls-verify=false,ca=tmp\ca.pem,cert=tmp\cert.pem,key=tmp\key.pem"
qnap
Successfully created context "qnap"
```

作成済みのコンテキストの接続パラメータを更新するコマンドは `docker context update` である。
また、`docker context inspect` で指定したコンテキストのパラメータを確認できる。

コンテキストの切り替えは `docker context use` である。

```
PS D:\test> docker context use qnap
qnap
```

```
PS D:\test> docker context list
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                             KUBERNETES ENDPOINT   ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   npipe:////./pipe/docker_engine                                    swarm
desktop-linux       moby                                                          npipe:////./pipe/dockerDesktopLinuxEngine
qnap *              moby                                                          tcp://192.168.0.128:2376
```

```
PS D:\test> docker ps
CONTAINER ID   IMAGE                   COMMAND   CREATED       STATUS       PORTS     NAMES
58a1d22b95e2   arm32v7/ubuntu:bionic   "bash"    2 hours ago   Up 2 hours             ubuntu-1
```

## その他
### QNAP の Docker のバージョン

QNAP の Docker のバージョンを確認してみた。
Windows の Docker のバージョン 20.10.7 に対して
QNAP の Docker は 20.10.3 で、
最新のバージョンではないものの、それほど古くはないことが確認できる。

```
PS D:\test> docker version
Client:
 Cloud integration: 1.0.17
 Version:           20.10.7
 API version:       1.41
 Go version:        go1.16.4
 Git commit:        f0df350
 Built:             Wed Jun  2 12:00:56 2021
 OS/Arch:           windows/amd64
 Context:           qnap
 Experimental:      true

Server:
 Engine:
  Version:          20.10.3
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       4c417df92a
  Built:            Fri Feb 26 04:03:02 2021
  OS/Arch:          linux/arm
  Experimental:     false
 containerd:
  Version:          v1.4.3
  GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc:
  Version:          1.0.0-rc93
  GitCommit:        12644e614e25b05da6fd08a38ffa0cfe1903fdec
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
  ```

### QNAP の Docker 証明書

QNAP からダウンロードした証明書、
ルート証明書 `ca.pem` と
クライアント証明書 `cert.pem`
の内容は次のようになっている。

#### ルート証明書 ca.pem

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

#### クライアント証明書 cert.pem

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

## まとめ

QNAP の Docker は、最初の環境構築のところこそ、
QNAP 固有の操作が必要であるものの、
Docker Hub のイメージをそのまま利用でき、
クライアント PC からの Docker 操作もできるので、
家庭で使う限りでは十分な環境でないかと思う。

