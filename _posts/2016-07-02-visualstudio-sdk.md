---
layout: post
title: Visual Studio に SDK ほかを追加する
date: 2016-07-02 10:00 +0900
---

## 概要
Visual Studio をデフォルト設定でインストールしたら
[T4 を msbuild から使うサンプル](https://msdn.microsoft.com/ja-jp/library/ee847423.aspx) が動かなかった。

Modeling SDK というのが必要だというのでインストールしたら SDK がないよと言われてしまう。

## 手順

### SDK (Visual Studio 2015 Software Development Kit) を追加する

+ コントロールパネルの [プログラム]-[プログラムと機能]-[プログラムのアンインストールまたは変更] を開く
+ インストールされているプログラムの一覧から Microsoft Visual Studio Community 2015 を探して [変更] を実行する (プログラム名はインストールしているエディションに応じて読み替える)
+ [Modify] ボタンをクリック
+ [Features] タブの Visual Studio Extensibility Tools Update 3 を選択して [Next] ボタンをクリック
+ これからインストールする機能の一覧が表示されるので Visual Studio 2015 Software Development Kit Update 3 を確認して [UPDATE] ボタンをクリック

### Modeling SDK for Visual Studio をインストールする

+ Microsoft のダウンロードセンターから [Modeling SDK for Microsoft Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48148) をダウンロードする
+ ダウンロードした vs_vmsdk.exe を実行してインストールする


