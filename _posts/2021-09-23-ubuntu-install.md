---
layout: post
title: Ubuntu サーバのインストール
date: 2021-09-23 11:00 +0900
image: /images/2021-09-20-preview.png
---
QNAPでのDocker運用にポートフォワードが安定しないという致命的な弱点があったので、
小型PCを購入してLinuxサーバを運用することにした。

## 小型PCの購入

Linux サーバをインストールして
モニターレス、キーボードレスで常時稼働させるので、
小型で消費電力が少なく、できれば安い PC が望ましい。

Amazon で CHUWI HeroBox Pro という小型PCが2万5千円程度で売ってたので購入。
memory 8GB、SSD 256GB と、この価格帯にしてはかなりよい感じのスペックである。

ハードのレビューについては
[こちらの記事](https://www.thun-techblog.com/index.php/blog/chuwi-herobox-pro-review-celeron-n4500/)
が、充実しているので紹介しておく (購入する前に見ておけばよかった)。

だいたい満足しているが、
ファンの音がちょっと気になるかな (先の記事によればファン制御の機能はないようだ)。

### 参考

+ [CHUWI "HeroBox Pro"のレビュー！Jasper Lake世代Celeron搭載でコスパ最高の最新ミニPC！ただし…※](https://www.thun-techblog.com/index.php/blog/chuwi-herobox-pro-review-celeron-n4500/)


## Ubuntu server のインストール

### インストールメディア作成とブート

Ubuntu の ISO イメージ
(ubuntu-20.04.3-live-server-amd64.iso)
をダウンロードして、
手持ちの USB メモリでブートメディアを作成する。
Windows 10 でのブートメディアの作成には
[rufus](https://rufus.ie/ja/)
というツールを使用。

![rufusによるブートメディア作成](/images/2021-09-20-ubuntu-rufus.png)

HeroBox Pro は起動時に ESC で BIOS 画面に、F7 で起動メディア選択メニューを出すことができる。
BIOS は AMI だった。
Quiet Boot を Enabled に、
Secure Boot を Disabled にしておく。

![Quiet Boot](/images/2021-09-20-ubuntu-bios-boot1.png)
![Secure Boot](/images/2021-09-20-ubuntu-bios-boot2.png)

Ubuntu のインストーラを rufus でコピーした USB メモリを HeroBox Pro に挿して起動、
すればよいはずだったのだが、ちょっとハマりポイントがあった。

古い USB メモリを使用したせいか、
HeroBox 全面の USB 3.0 コネクタではブートメディアとして認識されず、
背面の USB 2.0 コネクタに挿さなければいけなかった。
新しめの USB メモリでは前面でちゃんと認識されたので、
どちらが悪いのかわからないが、組み合わせの問題のようだ。

LANケーブルも挿しておく。

### デフォルト設定でインストール

画面の指示に従ってインストール。
ほとんどデフォルトのままで、
設定したのはキーボードの種類と初期アカウントくらい。

インストールが終わったら USB メモリを抜いて再起動。


## インストール後の設定
### ネットワーク設定の変更

デフォルト設定でインストールしたので
DHCP を使う設定になっている。

Ubuntu のネットワーク設定は `/etc/netplan` 以下にあって、
インストール直後の設定ファイルは
`00-installer-config.yaml`
のみで、中身は次のように dhcp4 を使ってインターフェースの設定を行うようになっている。
enp1s0 は HeroBox Pro のネットワークインターフェースの名前。

```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp1s0:
      dhcp4: true
  version: 2
```

サーバ運用なので IP アドレスは静的に設定したい、
が、その他の設定、デフォルトゲートウェイなど、
は DHCP から設定を持ってきたいところ。

そこで、同じディレクトリに `99-config.yaml` という名前で、
次のようなファイルを作成する。

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp1s0:
      addresses:
        - 192.168.0.11/24
      nameservers:
        search:
          - example.com
```

2つの設定ファイルは、辞書順に読み込まれるので、
ネットワークインターフェース enp1s0 に対して
dhcp4 を有効にし、かつ、IP アドレスを静的に指定した状態になる。

ここで使ってるルータの DHCP は DNS の検索パスを提供してくれないので、
nameservers の search についても静的に設定を行っている。

設定は `netplan apply` で反映させることができる。
確認して意図通りになっていることを確認したら、
再起動の場合についても確認しておく。

結果は、
静的に指定した IP アドレスと、
DHCP から取得した IP アドレスの両方が設定された状態となった
(DHCP の方には secondary dynamic のフラグがついている)。

```
herobox$ ip address
(略)
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
(略)
    inet 192.168.0.11/24 brd 192.168.0.255 scope global enp1s0
       valid_lft forever preferred_lft forever
    inet 192.168.0.141/24 brd 192.168.0.255 scope global secondary dynamic enp1s0
       valid_lft 80995sec preferred_lft 80995sec
(略)
```

不要な IP アドレスが余分に割り当てられた形となってしまったが、
DHCP クライアントとしての一貫性を保つためにはこの形がよいのかな。

### ホームディレクトリを autofs で NFS マウントする

NAS サーバにホームディレクトリがあるので、
autofs で NFS マウントする設定を行う。

autofs はインストールされていないので、
`apt install` で追加する。
NFS クライアントに必要な nfs-common も依存関係でインストールされる。

```
herobox$ sudo apt install autofs
```

`/home` にマウントさせるので、
次のような内容で
`/etc/auto.master.d/home.autofs`
を作成する。
拡張子が `.autofs` であればよいので、
`home.autofs` である必要はないのだが、
マウントポイントがわかりやすいようにしておく。

```
/home   /etc/auto.home
```

1列目の `/home` はマウントポイントで、
2列目の `/etc/auto.home` はマップファイル名である。

マップファイル `/etc/auto.home` には次のように、
key と option、location を記述する。
key はマウントポイントに対するサブディレクトリ名、
options はマウントコマンドに対するオプション、
locatioon はリモートファイルシステムのパス (NFS の場合) である。

```
foo    -rw,soft,intr   nas.example.com:/homes/foo
```

したがって、この例の場合、
`/home/foo` にアクセスした場合に
`mount -t nfs -o rw,soft,intr nas.example.com:/homes/foo`
相当が行われることになる。

`/etc/init.d/autofs reload` で動作確認したら、
再起動でも確認する。

ホームディレクトリには設定済みの `.ssh` ディレクトリがあるので、
そのまま SSH 公開鍵認証で接続できるようになった。
