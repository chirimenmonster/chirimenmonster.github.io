---
layout: post
title: Visual Studio の UI 日本語化
date: 2016-07-02 09:50 +0900
---
Visual Studio のメニューなどの表示を日本語に設定する

## 概要
Visual Studio 2015 をインストールした直後の状態では
メニューなどはすべて英語表記になっている。
Microsoft から日本語パックが出ているのでインストールすると日本語表示に切り替えることができる。

## 手順
+ メニュー [Tools] - [Options...] 
+ Options ウインドウ左側 [Environment] - [Internatinal Settings] を選択
+ Options ウィンドウ右側の Get additional languages のリンクをクリック
+ Microsoft Visual Studio 2015 Language Pack のダウンロードページ
+ [Select Language] のプルダウンメニューで [Japanese] を選択
+ ページの表示言語が日本語に変わる
+ [ダウンロード] ボタンで vs_langpack.exe をダウンロード
+ インストールの前に Visual Studio は終了しておく
+ vs_langpack.exe を実行する (表示されたウィンドウの表示言語が日本語でなかったらどこかで間違えてるのでダウンロードからやり直し)
+ [次へ] ボタンを押してインストールを続行する
+ Visual Studio を起動する
+ Options の [Environment] - [Internatinal Settings] に [日本語] が追加されている
　(Windows が日本語であれば [Same as Microsoft Windows] を選んでも可)
+ Visual Studio を再起動する

