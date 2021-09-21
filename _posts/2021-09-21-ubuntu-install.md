---
layout: post
title: Ubuntu install
date: 2021-09-21 10:30 +0900
---
QNAPでのDocker運用に致命的な弱点があったので、
小型PCを購入してLinuxサーバを運用することにした。

## 小型PC

Amazon で CHUWI Hero box という小型PCが2万5千円程度で売ってたので購入。
memory 8GB、SSD 256GB と、この価格帯にしてはかなりよい感じのスペックである。

ハードのレビューについては
[こちらの記事](https://www.thun-techblog.com/index.php/blog/chuwi-herobox-pro-review-celeron-n4500/)
が、充実しているので紹介しておく (購入する前に見ておけばよかった)。

### 参考

+ [CHUWI "HeroBox Pro"のレビュー！Jasper Lake世代Celeron搭載でコスパ最高の最新ミニPC！ただし…※](https://www.thun-techblog.com/index.php/blog/chuwi-herobox-pro-review-celeron-n4500/)


## Ubuntu server のインストール

### インストールメディア作成とブート

Ubuntu の ISO イメージをダウンロードして、
手持ちの USB メモリでブートメディアを作成する。

起動時に ESC で BIOS 画面に、F7 で起動メディア選択メニューを出すことができる。

古い USB メモリを使用したせいか、
HeroBox 全面の USB 3.0 コネクタではブートメディアとして認識されず、
背面の USB 2.0 コネクタに挿す必要があった。
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

サーバなので、固定アドレスを設定。

### ホームディレクトリを autofs で NFS マウントする

```
apt install autofs
```

