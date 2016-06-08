---
layout: post
title: MSYS2 起動バッチファイルがなくなった
date: 2016-06-08 13:10 +0900
---
MSYS2 更新したらショートカットで起動できなくなっていた

## 概要

MSYS2 を更新したら Windows のメニューから起動できなくなった。

msys2_shell.bat などの起動バッチファイルが削除されていて、
起動方法が変更されたみたい。


## 対策

とりあえず pacman -S base で更新してみる。

{% highlight dos %}
$ pacman -S base
:: 40 のパッケージがグループ base にあります:
:: リポジトリ msys
(略)
(18/40) 再インストール filesystem                  [#####################] 100%

      WARNING: the shell starting scripts have been unified. Please update your
      shortcuts to the following targets, otherwise they will STOP WORKING:

          * MSYS2_ROOT\msys2_shell.cmd -mingw32
          * MSYS2_ROOT\msys2_shell.cmd -mingw64
          * MSYS2_ROOT\msys2_shell.cmd -msys

(略)
{% endhighlight %}

メッセージに出てきたようにメニューのショットカットを修正すればいいみたい。

これまで環境ごとにバッチファイルが別になっていたのが
コマンドは共通でオプションを指定するようになったようだ。


## 感想

インストール中にメッセージでてもスクロールアウトするから気がつかんわな。
