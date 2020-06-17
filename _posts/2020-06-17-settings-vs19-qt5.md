---
layout: post
title: VS19 に Qt5 拡張機能をインストールする
date: 2020-06-17 21:10 +0900
---
Visual Studio Community 19 + Qt 5.12 の環境構築を行ったときのメモ その3

+ [その1 Visual Studio Community 19 のインストール]({{site.baseurl}}/2020/06/17/visualstudio19.html)
+ [その2 Qt 5.12.8 のインストール]({{site.baseurl}}/2020/06/17/qt5.html)
+ その3 VS19 に Qt5 拡張機能をインストールする

## はじめに

[前回]({{site.baseurl}}/2020/06/15/qt5.html)に引き続き、
今回は VS19 から Qt を利用するための拡張機能を設置する。
最新版の 5.15.0 じゃなく 5.12.8 にしてるのは作成するプラグイン (DLL) の本体側が 5.12 系列のため。

作成する環境は前回示したのと同様。

+ Visual Studio Community 19
+ Qt 5.12.8 MSVC 2017 64-bit

## 拡張機能 Qt Visual Studio Tools のインストール

### インストールする拡張機能を選択

Visual Studio を起動し、「拡張機能の管理」を実行する。
Visual Studio Marketplace から
Qt Visual Studio Tools を選択してインストールする。

![]({{site.baseurl}}/images/2020-06-15-171102-vs19ext.png)

### 拡張機能のインストール

拡張機能のインストーラが実行される。

![]({{site.baseurl}}/images/2020-06-15-171139-vs19ext.png)

![]({{site.baseurl}}/images/2020-06-15-171157-vs19ext.png)

拡張機能のインストールの完了。

![]({{site.baseurl}}/images/2020-06-15-171618-vs19ext.png)

### Qt Visual Studio Tools の設定

VS のメニューから [拡張機能]->[Qt VS Tools]->[Qt Options] を実行する。

![]({{site.baseurl}}/images/2020-06-15-171710-vs19ext.png)

VS で使用する Qt のセットを選択する。
[Add] ボタンを押して新規に追加する。

![]({{site.baseurl}}/images/2020-06-15-171804-vs19ext.png)

[Path] 欄の右端の [...] ボタンで Qt のフォルダーを選択する。

![]({{site.baseurl}}/images/2020-06-15-171825-vs19ext.png)

Qt 5.12.8 の msvc2017_64 を選択。

![]({{site.baseurl}}/images/2020-06-15-171933-vs19ext.png)

適合するフォルダを選択すると、
"Version name" フィールドにデフォルトの名称が入る。

![]({{site.baseurl}}/images/2020-06-15-171956-vs19ext.png)

今回使用するのは最新版ではないので、
区別しやすいように Qt のバージョンも名称に含めるよう修正。

![]({{site.baseurl}}/images/2020-06-15-172029-vs19ext.png)

複数のバージョンを使用する場合は、
デフォルトのバージョンをここで指定する。
(実際に使用するはプロジェクトの方で変更できる)

![]({{site.baseurl}}/images/2020-06-15-172052-vs19ext.png)

## おわり

これで VS19 から Qt を使用する準備ができた。

