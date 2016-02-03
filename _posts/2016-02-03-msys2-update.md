---
layout: post
title: MSYS2 のアップデート
date: 2016-02-03 10:50:00 +0900
---
MSYS2 のアップデートをしたときのメモ

# 手順

`update-core` コマンドを実行する。

{% highlight sh %}
$ update-core
{% endhighlight %}

更新があったら MSYS2 を一旦再起動する (重要)。
終了するときには Alt-F4 あるいはクローズボタンを使用する。

`pacman -Su` を実行する。

{% highlight sh %}
$ pacman -Su
{% endhighlight %}


で、なぜかエラー。

    (275/275) キーリングのキーを確認                   [#####################] 100%
    (275/275) パッケージの整合性をチェック             [#####################] 100%
    エラー: mingw-w64-i686-oniguruma: signature from "Alexey Pavlov (Alexpux) <alexpux@gmail.com>" is invalid
    :: ファイル /var/cache/pacman/pkg/mingw-w64-i686-oniguruma-5.9.6-1-any.pkg.tar.xz は破損しています (無効または破損したパッケージ (PGP 鍵))。
    ファイルを削除しますか? [Y/n]
    エラー: 処理を完了できませんでした (無効または破損したパッケージ)
    エラーが発生したため、パッケージは更新されませんでした。

一時的にデータベースが壊れているだけか、
こちらの環境が壊れているのか判断できないな。

時間をおいてやり直すか。

...

あきらめて、msys2を一旦アンインストールしてから再インストールした。

{% highlight sh %}
$ update-core
(msys2を一旦終了して再実行)
$ pacman -Su
$ pacman -S base-devel
$ pacman -S mingw-w64-x86_64
$ pacman -S mingw-w64-i686
$ pacman -S mingw-w64-x86_64-toolchain
$ pacman -S mingw-w64-i686-toolchain
{% endhighlight %}

特にエラーとかなくインストールできたっぽい。


# 参考
+ [Qiita:MSYS2における正しいパッケージの更新方法](http://qiita.com/k-takata/items/373ec7f23d5d7541f982)
+ [Qiita:MSYS2でパッケージのダウンロードに失敗するときの対処](http://qiita.com/k-takata/items/fcb2f1f9ca564fd78597)