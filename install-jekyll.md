---
title: install jekyll
layout: default
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

コマンド:

    cd C:\RubyDevKit
    ruby dk.rb init
    ruby dk.rb install

Ruby がインストールされている場所を自動的に探して設定される。


## Jekyll のインストール

### Jekyll 本体

Jekyll は Ruby のパッケージ管理システム  (RubyGems) で管理される。
以下のコマンドを実行すると自動でダウンロードしてインストールされる。
Proxy 環境では環境変数 (http_proxy) かオプション (-p)
でプロキシサーバを指定する必要がある。

    gem install jekyll


### シンタックスハイライト用のツール

Rouge と Pygments の2種類ある。

ちなみに GitHub 内部で使われているのは Pygments の方。
ただし Pygments は Python 2.7 が必要 (3.x では動かない) なので、
さらに Python をインストールする必要がある。

Rouge は Ruby で動くので追加のツールは必要ない。
ただしプレビュー時に別の設定ファイルを指定する必要がある。

インストールが楽なのは Rouge なのでこちらにする。
もしかしたら Rouge は Jekyll と一緒にすでに入っていたかもしれない。

    gem install rouge


### 監視用のツール

プレビューするときは、
変換してローカルにWebサーバを立ち上げてブラウザから接続する形になる。

ソースを更新したらまた変換して…と面倒だが、
Wdm はその辺のところをやってくれる。

ここまで来たら四の五の言わずに黙って入れよう。

    gem install wdm


## ディレクトリツリーの構築

GitHub pages 用の空のフォルダのひとつ上で以下のコマンドを実行する。

    jekyll new (フォルダ名)

## プレビュー


フォルダに移動して

    jekyll build



_config-win.yml 

    highlighter: rouge

Windows 用の設定ファイルを追加して、ビルド、プレビュー。

    jekyll build --config _config.yml,_config-win.yml
    jekyll serve --config _config.yml,_config-win.yml


http://localhost:4000/ で接続する。

