---
layout: post
title: TeX Live のインストール
date: 2016-03-04 11:50:00 +0900
---
TeX Live のインストール

参考: [TeX Wiki:TeX Live/Windows](https://texwiki.texjp.org/?TeX%20Live%2FWindows)

## ダウンロードとインストール

[TUG:Installing TeX Live over the Internet](https://www.tug.org/texlive/acquire-netinstall.html)
から install-tl-windows.exe をダウンロードして実行。

プロキシの設定をする必要があるので 'Unpack only' を選択して
ファイルの展開だけを行う。
展開先のフォルダを指定して 'Install' ボタンを押すと指定のフォルダにファイルが展開される。 

プロキシの設定は、
先ほど展開したフォルダにある tlpkg\installer\wget に
.wgetrc という名前で設定ファイルを作成する。
内容は下のような感じ。
ここではプロキシサーバを http://proxy.example.net:8080 に設定している。

```
http_proxy=http://proxy.example.net:8080
ftp_proxy=http://proxy.example.net:8080
use_proxy=on
```

プロキシ経由ではインストール時にオプション `--no-persistent-downloads`
をつけないと時間がかかるそうなので、
コマンドプロンプトから展開したフォルダにある install-tl-windows.bat を
オプションつきで実行する。

```
install-tl-windows.bat --no-persistent-downloads
```

あとは指示に従いインストールを実行する。

参考: [プロキシ環境下で Windowsマシンに TeX Live を入れる際のメモ](http://ichiro-maruta.blogspot.jp/2012/04/windows-tex-live.html)
