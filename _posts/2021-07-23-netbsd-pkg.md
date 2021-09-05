---
layout: post
title: NetBSD の pkgsrc 設定
date: 2021-07-23 13:00 +0900
---
先日インストールした NetBSD 9.2 にアプリケーションをインストールしてなかったので
pkgsrc を入れた。

```
$ ftp ftp://ftp.NetBSD.org/pub/pkgsrc/pkgsrc-2021Q1/pkgsrc.tar.gz
$ tar -xzf pkgsrc.tar.gz -C /usr
```

公式ドキュメントも確認したけど、
今でもアプリケーションは
pkgsrc で make してインストールするのが
基本らしい。
NetBSD だとサポートしないといけないアーキテクチャが多いからそうなるか。

とはいえ、
バイナリパッケージを仕組みも整備されていて
公式ドキュメントによれば pkgin というツールを使うんだそうだ。

以前からある pkg_add との役割の違いがわからないが使ってみる。


## pkgin のインストール

/root/.profile に PKG_PATH の設定を追加する。
デフォルトのインストールでは /root/.profile に下記のようなコメントアウトされた設定があるので、
先頭 `#` を削除して有効にすればよい。

```
$ grep PKG_PATH /root/.profile
#export PKG_PATH="https://cdn.NetBSD.org/pub/pkgsrc/packages/NetBSD/$(uname -p)/$(uname -r|cut -f '1 2' -d.|cut -f 1 -d_)/All"
```

PKG_PATH は arch やバージョンに依存した項目があって、
実際に反映されるのは次のようになる。

```
$ sudo -i sh -c 'echo PKG_PATH=$PKG_PATH'
We recommend that you create a non-root account and use su(1) for root access.
PKG_PATH=https://cdn.NetBSD.org/pub/pkgsrc/packages/NetBSD/x86_64/9.2/All
```

pkgin をインストールする。

```
$ sudo -i pkg_add pkgin
We recommend that you create a non-root account and use su(1) for root access.
pkg_add: Warning: package `pkgin-20.12.1' was built for a platform:
pkg_add: NetBSD/x86_64 9.0 (pkg) vs. NetBSD/x86_64 9.2 (this host)
pkg_add: Warning: package `pkg_install-20210308' was built for a platform:
pkg_add: NetBSD/x86_64 9.0 (pkg) vs. NetBSD/x86_64 9.2 (this host)
===========================================================================
$NetBSD: MESSAGE,v 1.7 2017/01/09 07:01:33 sevan Exp $

You may wish to have the vulnerabilities file downloaded daily so that it
remains current. This may be done by adding an appropriate entry to the root
users crontab(5) entry. For example the entry

(snip)
```

```
# pkgin -f search bash
reading local summary...
processing local summary...

/!\ Warning /!\ i386 doesn't match your current architecture (x86_64)
You probably want to modify /usr/pkg/etc/pkgin/repositories.conf.
Still want to proceed ? [y/N] y
[1]   Alarm clock             pkgin -f search bash
```

https://mail-index.netbsd.org/netbsd-users/2015/10/09/msg017030.html

