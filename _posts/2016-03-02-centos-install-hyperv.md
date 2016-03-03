---
layout: post
title: CentOS 6 を Hyper-V にインストール
date: 2016-03-02 09:30:00 +0900
---
いまさら感はあるけれども CentOS 6 を Hyper-V にインストールしたので
そのときの記録。

### はじめに

Linux 用のちょっと古めのバイナリを動かしておく必要ができたのだけれど、
利用可能な Linux サーバがなくて、
だけど Windows Server なら使える状態だったので
Hyper-V 上に専用の Linux 環境を整えることにした。

最新の Linux である必要はなく、他のサーバアプリを動かすこともないので
枯れてそうな CentOS 6 をインストールや管理の手間が少なそうな構成で
動かすことが目標。


### ISO イメージのダウンロード

[ミラーサイト](http://isoredirect.centos.org/centos/6/isos/x86_64/) から
CentOS 6.7 のインストールイメージ (64bit版) をダウンロードする。
複数の種類のISOイメージがあるけれど、
ここでは
[CentOS-6.7-x86_64-minimal.iso](http://ftp.jaist.ac.jp/pub/Linux/CentOS/6.7/isos/x86_64/CentOS-6.7-x86_64-minimal.iso)
を選んだ。

minimal を選んだのは、
インストール直後では必要最小限のパッケージのみあればいいのと、
インストール過程でネットワークアダプタ (仮想) が
有効になっているか不明だったから。
この条件でサイズの大きいフルパッケージは除外、netinstall も除外で
残るのは minimal。


### Hyper-V での仮想マシン作成と CentOS のインストール

特に記すことはない。

普通に仮想マシンを作成して、
CentOS の ISO イメージをマウントした状態で起動すると
インストールが始まるので指示に従うだけ。

インストールが完了して再起動した段階では、
ユーザは root しか作成されておらず、ネットワークの設定もされていない。

man もインストールされていないし、
当然ながら yam も (ネットワークがつながってないので) 動作しない。


### コンソール画面サイズの変更

インストール後の状態では CentOS の画面サイズが大きすぎて
Hyper-V のウィンドウに納まりきらない。

これでは vi を使うのにも難儀するので変更する必要がある。

そこで
[ex1-lab:Hyper-V で CentOS のコンソールサイズが大きすぎる](http://ex1.m-yabe.com/archives/1108)
を参考に、
コンソールの画面サイズを変更する。

/boot/grub/menu.lst のカーネル起動オプションに
`video=hyperv_fb:640x480` を追加した。

reboot すると有効になる。


### ホスト名とネットワークの設定

ホスト名は /etc/sysconfig/network の HOSTNAME で指定する。

IPアドレスなどは /etc/sysconfig/network-scripts/ifcfg-eth0 で指定する。

BOOTPROTO を dhcp から none に変更、
IPADDR, NETMASK, GATEWAY を (静的に) 設定。

DNS 関連を /etc/resolv.conf で設定。
nameserver, domainname
正しくは ifcfg-eth0 の DNS1, DNS2, DOMAIN で指定する?

`service network restart` で反映される
(ホスト名は反映されなかったような気がする)。


### yum update

ネットワークの設定ができたので、`yum update` を実行しておく。

man がないのは不便なので `yum install man` も実行しておく。


### ssh

ここまでの設定で、すでに sshd は動いていて root でパスワードログインできる。

一般ユーザで ssh ログインして sudo で作業するように以下を設定する。

+ useradd(8) による作業用ユーザの登録
+ 作業用ユーザを wheel グループに追加
+ visudo(8) により wheel グループに sudo を許可する
+ 作業用ユーザの ssh 用公開鍵を登録 (~/.ssh/authorized_keys)
+ ssh でパスワードログインを拒否する設定 (/etc/ssh/sshd_config PasswordAuthentication)
+ ssh で root ログインを拒否する設定 (/etc/ssh/sshd_config PermitRootLogin)


### 実行してみる

/lib/ld-linux.so.2: bad ELF interpreter: No such file or directory

[SLUMBERS:CentOS 64bit で /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory って怒られた際にやったこと](http://slumbers99.blogspot.jp/2012/04/centos-64bit-libld-linuxso2-bad-elf.html)
によれば CentOS 64bit で 32bit コマンドを動かそうとしたかららしい。

`yum install ld-linux.so.2` で必要なパッケージをインストールしてくれるそうだ。

動くようになった。すごいね。


### MAC アドレスの変更

Hyper-V 側で MAC アドレスを変更したら eth0 が eth1 になってしまった。
ifcfg-eth0 を修正してもダメ。

他にも修正しなければいけないところがあったもよう。

参考: [centos6.2のMACアドレス変更手順](http://spring-mt.tumblr.com/post/27897296877/centos62%E3%81%AEmac%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E5%A4%89%E6%9B%B4%E6%89%8B%E9%A0%86)


### ポートの開放

インストール後は ssh (TCP 22) くらいしかポートが開放されていない。設定は /etc/sysconfig/iptables で行う。

ファイルの修正が終わったら、`service iptables restart` で設定を反映させる。

参考: [CentOS に iptables の設定を行う (IPv4)](http://www.websec-room.com/2014/01/12/1511)
