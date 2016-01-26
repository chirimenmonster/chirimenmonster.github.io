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

### Ruby DevKit のインストール

+ [RubyInstallers](http://rubyinstaller.org/downloads/) の
DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe を実行
+ 自己展開ファイルになっているので展開場所に C:\RubyDevKit を指定
+ コマンドプロンプトで以下のコマンドを実行


	cd C:RubyDevKit #
	ruby dk.rb init
	ruby dk.rb install


## Jekell のインストール

    gem install jekyll

Rouge にするか Pygments にするか決める。
どちらも syntax highlight を行うためのもの。
基本的に Rouge は Pygments 互換のライブラリだと考えてよさそう。
Rouge は Ruby で完結しているけど Pygments だとさらに Python が必要。
インストールが楽なのは Rouge なのでこちらにする。

```
    gem install rouge
    gem install wdm
    jekyll new dirname
```

add line to _config.yml

    highlighter: rouge
