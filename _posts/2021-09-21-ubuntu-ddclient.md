---
layout: post
title: Ubuntu ddclient
date: 2021-09-21 10:30 +0900
---
外部から自宅サーバを操作するのには
Cloudflare で DDNS やるのが手っ取り早そうだった。

## はじめに

自宅に設置した Ubuntu サーバを外部から ssh できるようにするには
いろいろな方法があると思うのだけれど、

+ グローバルIP (IPv4) は動的に割り当てられる (IPv6 は動的なのか? 要確認)
+ プロバイダから貸与されてるルータでポートフォワード設定は可能
+ ドメインは取得していて、ネームサーバを Cloudflare に置いている
+ 外部に (仮想) サーバを借りていて、それなりに融通が利く

という環境で、
あまり手間をかけないで実現するには、
ルータで ssh のポートを Ubuntu サーバに転送しておいて、
Cloudflare を DDNS サーバとして運用することで対応するのが簡単そうである。

ポートを公開すると、侵入を試みるアクセスが増えそうだけど、
転送許可するアドレスを、外部のサーバに限定しておけばよいだろう。

## ddclient

ちょうど
[Cloudflare を ddclient で DDNS 化する](https://zenn.dev/akaregi/articles/4a0db32a4d40a7)
という記事があったので読んでみると、
Ubuntu 20.04 LTS (Focal Fossa) の apt で提供されている ddclient 3.8.x では
現在の Cloudflare に対応できず ddclient 3.9.x が必要らしい。

なんてこった。

だが、先の記事では
[Launchpad](https://launchpad.net/ubuntu/+source/ddclient)
から
Groovy 用の ddclient 3.9.1 の deb を `dpkg -i` でインストールすればよい、とある。

Groovy とは Ubuntu 20.10 (Groovy Gorilla) のことだろう。
Launchpad には Ubuntu 21.04 (Hirsute Hippo) 用の 3.9.1-7 と
Ubuntu 20.04 LTS (Focal Fossa) 用の 3.8.3-1.1ubuntu1 が提供されていて
Groovy 用は見当たらない。





### 参考

+ [Cloudflare を ddclient で DDNS 化する](https://zenn.dev/akaregi/articles/4a0db32a4d40a7)

