---
layout: post
title: Jekyll で GitHub Pages をつくる
date: 2016-01-27 11:50:00 +0900
---
Windows PC に [Jekyll](https://jekyllrb.com/) をインストールして
[GitHub Pages](https://pages.github.com/) で公開するまでの手順。

# 動機

無料のホスティングサービスに [Google Site](https://sites.google.com/)
があるんだけれど、正直なところちょっと使いにくい。
お仕着せのオンラインエディタを使ってカジュアルに書くか、
html をゴリゴリ書いてアップロードするかの2択になってしまう。
コード (の断片) とかちょくちょく載せたい時には Google Site の
オンラインエディタはちょっと力不足なんだよね。

もうちょっとこう、たとえば [Qiita](https://qiita.com/) とかみたいに
Markdown 記法が使えて、
できればコードのシンタックスハイライトができるといいんだけど。

なら Qiita でいいじゃん、ていう人もいるかもしれない。
でも書く内容が Qiita の趣旨と違うことも多いので残念ながらパス。
あとほかでは [Tumblr](https://www.tumblr.com/) は UI が超絶なじまなかったし、
サイトの全体的な SNS っぽさが肌に合いそうもない。
[はてな](http://www.hatena.ne.jp/) も SNS っぽいし広告がちょっとな。
って思ってるところに、
[GitHub Pages](https://pages.github.com/) も検索に引っかかった。

実は GitHub Pages は試したことがあるのだが、
オンラインでマークダウン記法が使えないということで以前見送っていた。
でも、GitHub 本体の方のシンタックスハイライトやマークダウンは
気に入っているので、それらが利用できるなら、ということで試して見ることにした。

ざっと調べてみると、GitHub Pages でマークダウンを利用する場合は、
まずローカルに GitHub Pages のエンジンと同様の環境を構築し、
書いた内容をブラウザなどで確認してからソースを GitHub にプッシュする。
そうすればあとはサイトの方で HTML に変換して公開してくれる、という仕組みらしい。

なんてめんどくさいんだ。

プレビューのサービスさえ提供してくれれば、
ローカルでこんなことしなくてもいいんじゃないか、
と思いながら作業した結果がこちらです。


# 環境構築

普段遣いの端末は Windows なので Windows に環境を構築する。
といっても今では UNIX 系はサーバに SSH でつなぐくらいしか使ってない。

で、マークダウンで書いたソースを HTML に変換するのに Jekyll という
ツールを使うらしのだが、これが Ruby で書かれている。

…帰りたくなってきた。

いや、スクリプト言語としての Ruby が悪いわけじゃないけど、
ツールを使いたい、と言うためだけの理由で
Ruby のシステム一式を導入しないといけないというのは気が重い。
そもそも昔 FreeBSD の portupgarde が Ruby で書かれていたせいで、
Ruby がアップデートするたび何かしらはまっていたという嫌な思い出しかないしな。

まあここまできたら進むしかない。

参考: [Run Jekyll on Windows](http://jekyll-windows.juthilo.com/)


## Ruby のインストール

まずは Ruby をインストールする。
Windows 用 Ruby のインストーラは
[RubyInstallers](http://rubyinstaller.org/downloads/)
から入手できる。

複数のバージョンがあるが、
新しいのが良かろうと思ってこの時点で最新の Ruby 2.2.3 をインストールした。
後でよく見たらページの右側に 2.1.x が安定版でおすすめだと
(英語で) 書いてあった。

いやいや、ならファイルの 2.1.7 のところにも STABLE って書いとけよ。

必要なのは Ruby 本体と Devkit。
Devkit は複数のバージョンに対応している。

### Ruby 本体

+ [RubyInstallers](http://rubyinstaller.org/downloads/) の
Ruby 2.2.3 (x64) をダウンロードして管理者権限で実行
+ 今後のことを考えてパスは通すようにしておく

### Ruby DevKit

+ [RubyInstallers](http://rubyinstaller.org/downloads/) の
DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe を実行
+ 自己展開ファイルになっているので展開場所に C:\RubyDevKit を指定
+ コマンドプロンプトで以下のコマンドを実行する

```batch
cd C:\RubyDevKit
ruby dk.rb init
ruby dk.rb install
```

Ruby がインストールされている場所を自動的に探して設定される。


## Jekyll のインストール

### Jekyll 本体

Jekyll は Ruby のパッケージ管理システム  (RubyGems) で管理される。
以下のコマンドを実行すると自動でダウンロードしてインストールされる。
Proxy 環境では環境変数 (http_proxy) かオプション (-p)
でプロキシサーバを指定する必要がある。

```batch
gem install jekyll
```

### シンタックスハイライト用のツール

Rouge と Pygments の2種類ある。

ちなみに GitHub 内部で使われているのは Pygments の方。
ただし Pygments は Python 2.7 が必要 (3.x では動かない) なので、
さらに Python をインストールする必要がある。

Rouge は Ruby で動くので追加のツールは必要ない。
ただしプレビュー時に別の設定ファイルを指定する必要がある。

インストールが楽なのは Rouge なのでこちらにする。
もしかしたら Rouge は Jekyll と一緒にすでに入っていたかもしれない。

```batch
gem install rouge
```

### 監視用のツール

プレビューするときは、
変換してローカルにWebサーバを立ち上げてブラウザから接続する形になる。

ソースを更新したらまた変換して…と面倒だが、
Wdm はその辺のところをやってくれる。

ここまで来たら四の五の言わずに黙って入れよう。

```batch
gem install wdm
```


## 最初のビルド

### ディレクトリツリーの構築 (初期設定)

Jekyll でビルドするのに設定ファイルとかフォルダ構成とかいろいろ必要になる。
自前で用意するのは大変だけど `jekyll new` というコマンドを実行すると初期構成をやってくれるので、
あとはそれをもとにカスタマイズしていくのがよい。

`jekyll new` は引数としてパスを要求する。パスは存在しないか空のフォルダでなければならない。
新規にフォルダを作成するなら、`jekyll new` で環境を作ってから GitHub にプッシュすればいいし、
空の GitHub Pages のプロジェクトフォルダに対して `jekyll new` してもいい。

```batch
jekyll new (フォルダ名)
```

これでビルドに必要なフォルダ構成と、設定ファイル、ソースが設置される。


### ビルドの実行

ビルドはプロジェクトフォルダで以下のコマンドを実行する。

```batch
jekyll build
```


生成された HTML ファイルは _site に置かれる。

### プレビュー

以下のコマンドを実行するとプレビュー用の web サーバが起動する。
サーバはフォアグラウンドで実行されるので、不要になったら Ctrl-C とかで終了させる。

```batch
jekyll serve
```

ブラウザからは http://localhost:4000/ にアクセスする。
もちろんサーバが終了してたら接続できない。


## カスタマイズ

### シンタックスハイライト

シンタックスハイライトのツールとして rouge を選択した。
本来なら _config.yml に `highlighter: rouge` を設定するのだが、
そのままだと GitHub に _config.xml をプッシュしたときに (GitHub は Pygments の方を使うので) エラーになってしまう。

そこで、別の設定ファイルとして _config-win.yml を新規に作成し、そこで rouge を指定する。

```batch
highlighter: rouge
```

ビルド、プレビューの時には jekyll のオプション --config で、_config.yml と _config-win.yml を指定する。
このようにすれば、_config-win.yml で記述した部分が優先される。 

```batch
jekyll build --config _config.yml,_config-win.yml
jekyll serve --config _config.yml,_config-win.yml
```

プレビュー用のサーバが起動している間は、ソースを更新すると自動的に再ビルドしてくれる。
設定ファイルの更新は対象外なのでいったん終了する必要がある。


## プッシュ = デプロイ

プレビューを確認したら GitHub にプッシュすると、公開される。

ただし、ローカルでは成功してるのに GitHub の方でエラーになって失敗することもある。
成功したか失敗したかはメールで通知がくるが、エラーの原因を特定するにはほぼほぼ役に立たない。
このあたりはなんとかしてほしいところ。
