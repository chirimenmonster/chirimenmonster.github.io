---
layout: post
title: Ubuntu install
date: 2021-09-21 10:30 +0900
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

HeroBox Pro は起動時に ESC で BIOS 画面に、F7 で起動メディア選択メニューを出すことができる。

Ubuntu のインストーラを rufus でコピーした USB メモリを HeroBox Pro に挿して起動、
すればよいはずだったのだが、ちょっとハマりポイントがあった。

古い USB メモリを使用したせいか、
HeroBox 全面の USB 3.0 コネクタではブートメディアとして認識されず、
背面の USB 2.0 コネクタに挿さなければいけなかった。
新しめの USB メモリでは前面でちゃんと認識されたので、
どちらが悪いのかわからないが、組み合わせの問題のようだ。

LANケーブルも挿しておく。

### Ubuntu のインストール

画面の指示に従ってインストール。
ほとんどデフォルトのままで、
設定したのはキーボードの種類と初期アカウントくらい。

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

同じディレクトリに `99-config.yaml` という名前で、
次のようなファイルを作成する。

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp1s0:
      addresses:
        - 192.168.0.11/24
```

2つの設定ファイルは、辞書順に読み込まれるので、
ネットワークインターフェース enp1s0 に対して
dhcp4 を有効にし、かつ、IP アドレスを静的に指定した状態になる。

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



サーバなので、固定アドレスを設定。

### ホームディレクトリを autofs で NFS マウントする

```
herobox$ sudo apt install autofs
```

`/etc/auto.master.d/home.autofs`

```
/home   /etc/auto.home
```

`/etc/auto.home`

```
foo    -rw,soft,intr   nas.example.com:/homes/foo
```
