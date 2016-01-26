---
title: install jekyll
layout: default
---
参考 http://jekyll-windows.juthilo.com/

## Ruby のインストール

### Ruby 本体

+ RubyInstallers http://rubyinstaller.org/downloads/ の Ruby 2.2.3 (x64) を実行

### Ruby DevKit のインストール

+ DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe を実行
+ 展開場所に C:\RubyDevKit を指定

	cd C:\RubyDevKit
    ruby dk.rb init
    ruby dk.rb install


## Jekell のインストール

    gem install jekyll

Rouge にするか Pygments にするか決める。
どちらも syntax highlight を行うためのもの。
基本的に Rouge は Pygments 互換のライブラリだと考えてよさそう。
Rouge は Ruby で完結しているけど Pygments だとさらに Python が必要。
インストールが楽なのは Rouge なのでこちらにする。

    gem install rouge
    gem install wdm
    jekyll new dirname

add line to _config.yml

    highlighter: rouge
