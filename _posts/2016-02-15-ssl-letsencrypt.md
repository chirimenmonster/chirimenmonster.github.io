---
layout: post
title: Let's Encrypt で SSL 証明書取得
date: 2016-02-15 13:30:00 +0900
---
[Let's Encrypt](https://letsencrypt.org/) で SSL 証明書を取得するまでのメモ

## 作業内容

### クライアントのインストール

適当なディレクトリ (作業用) で以下のコマンドを実行する。


{% highlight session %}
$ git clone https://github.com/letsencrypt/letsencrypt
$ cd letsencrypt
{% endhighlight %}

Let's Encrypt クライアントをインストールする、
の前に CentOS 6 の場合はあらかじめ EPEL リポジトリを有効にしておく必要があった。

{% highlight session %}
$ sudo rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
{% endhighlight %}

Let's Encrypt クライアントをインストールする。

{% highlight session %}
$ ./letsencrypt-auto
{% endhighlight %}

なんか ~/.local/share/letsencrypt にインストールされた、
けどこのあとも ./letencrypt-auto を実行するのでいいらしい。

### SSL 証明書の取得

コマンドは `./letsencrypt-auto certonly` で、
稼働中の Web サーバがある場合にはプラグイン指定オプション `--webroot` をつける。
そうするとドメイン使用権者認証が Web サーバに置いたファイルに
アクセスする方式となる
(認証ファイルを設置するのは自動)。


### テストサーバからテスト用の証明書を取得する

本番前にまずはテスト。

オプション `--test-cert` をつけるとテスト用の証明書を取得する
(テスト証明書配布用のサーバに接続するらしい)。
本番証明書の取得は回数制限があるそうなので、
手順が飲み込めるまではテスト証明書で練習しておく。

{% highlight session %}
$ ./letsencrypt-auto certonly --webroot --test-cert -w (ドキュメントルート) -d (ドメイン)
{% endhighlight %}

実行すると /etc/letsencrypt/live/(ドメイン) の中に
privkey.pem, fullchain.pem, chain.pem, cert.pem
が作成された。
これらはシンボリックリンクで、
実体は /etc/letsencrypt/archive/(ドメイン) に置かれている。
ディレクトリは root のみアクセス可能。
一般ユーザで実行したから内部で sudo か何かを実行しているのだろう。

名称から容易に想像がつくが、privkey.pem が秘密鍵、他は証明書だ。
Apache なら秘密鍵 privkey.pem と
全部入りの証明書 fullchain.pem を使うのでよい。

{% highlight text %}
SSLCertificateKeyFile /etc/letsencrypt/live/(ドメイン)/privkey.pem
SSLCertificateFile /etc/letsencrypt/live/(ドメイン)/fullchain.pem
{% endhighlight %}

Apache に graceful start で新しい SSL 設定を読ませる。

{% highlight session %}
$ sudo apachectl graceful
{% endhighlight %}

実際は CentOS 6 の SCL リポジトリの Apache24 をインストールしているので
以下のコマンドを打っている。

{% highlight session %}
$ sudo scl enable httpd24 'apachectl graceful' 
{% endhighlight %}

これで取得した証明書を使って https で接続できるわけだが、
テスト用の証明書なのでブラウザに読ませると不正な証明書扱いになる。
ちなみに issuer は happy hacker fake CA となっている。

{% highlight session %}
$ sudo openssl x509 -in cert.pem -noout -issuer
issuer= /CN=happy hacker fake CA
{% endhighlight %}


### 本番証明書を取得する

いよいよ本番証明書の取得である。

最終的には証明書取得のプロセスを自動化しないといけないので、
対話的な操作が不要な手順を確認しておく。

オプション `-n` は対話的な問い合わせを行わない指定 (`--no-interractive`)。
オプション `--aggree-tos` は
Let's Encrypt のサービス利用規約 (terms of service) に同意する指定。

{% highlight session %}
$ ./letsencrypt-auto certonly --webroot -w (ドキュメントルート) -d (ドメイン) -m (メールアドレス) -n --agree-tos
{% endhighlight %}

この指定でいけるかと思ったのだが、
さっき取得したテスト証明書があったので更新扱いになるようだ。
なので強制更新するオプション `--force-renewal` も指定する。

{% highlight session %}
$ ./letsencrypt-auto certonly --webroot -w (ドキュメントルート) -d (ドメイン) -m (メールアドレス) -n --agree-tos --force-renewal
$ sudo scl enable httpd24 'apachectl graceful'
{% endhighlight %}

実運用のときはオプション `--keep` で期限切れが近いときだけ
更新してくれるようだが、
具体的に何日前から更新してくれるのかが不明。
[10 日だといってる人も 30 日だという人もいる](https://community.letsencrypt.org/t/what-is-the-behaviour-of-the-keep-option/7334)。
それによって cron で回す頻度が変わると思うんだけど。

毎日が前提?


## 参考
+ [Let's Encrypt](https://letsencrypt.org/)
+ [Let's Encrypt 総合ポータル](https://letsencrypt.jp/)
