---
layout: post
title: Windows にとりあえずインストールするソフト
date: 2016-01-27 13:55:00 +0900
---
新しい Windows PC を手にしたときにまずインストールするソフトとその設定のメモ

## インストール一覧

### ソフトのリスト
* Firefox
* Thunderbird
* PuTTY
* paint.net
* emacs
* MacType

### フォントのリスト
* IPAexフォント
* 源真ゴシック
* Ricty

## 設定

### Firefox

#### フォントのインストールと設定

参考: [ブラウザのフォントを綺麗にする方法メモ](http://prpr.hatenablog.jp/entry/2015/09/06/%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6%E3%81%AE%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88%E3%82%92%E7%B6%BA%E9%BA%97%E3%81%AB%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95%E3%83%A1%E3%83%A2%EF%BC%88Windows%E4%B8%8A%E3%81%AEF) （Windows上のFirefox、Chrome）

* フォント [源真ゴシック](http://jikasei.me/font/genshin/) と [IPAex明朝](http://ipafont.ipa.go.jp/) をインストール。
* アドオン [Anti-Aliasing Tuner](https://addons.mozilla.org/ja/firefox/addon/anti-aliasing-tuner/?src=api) をインストール。

##### フォントの設定

| 種別 | フォント |
|:---:|:---:|
| 明朝体 | IPAex明朝 |
| ゴシック | 源真ゴシック |
| 等幅 | 源真ゴシック等幅 |

##### Anti-Aliasing Tuner の設定

| パラメータ | 値 |
|:----:|:----:|
| Gamma | 1200/1000 |
| Enhanced Contrast | 80% |
| Clear Type Level | 100% |
| Anti-aliasing Mode | Clear Type |
| Rendering Mode | Natural Symmetric |

![Anti-Aliasing Tuner の設定](/images/2016-01-27-firefox-addon-antialiasingtuner.png)


### Thunderbird

指定するフォントは Firefox と同じ。

フォントのスムージングを有効にするため、設定エディタで以下の値を設定する。

|パラメータ|値|
|:---|:---|
|gfx.font_rendering.directwrite.enabled|true|
|gfx.font_rendering.cleartype_params.rendering_mode|5|

### MacType

一部のフォント (例えば Ricty) を WIndows で使おうとすると、線の一部が消えてしまい使用に耐えないので MacType を適用する。一部のアプリケーションにのみ MacType を適用するため、設定ファイルで `UseInclude=1` を指定する。

使用する設定ファイル (例えば Default.ini など） に記述する。

`UseInclude=1` は `IncludeModule` で指定した実行ファイルにのみ MacType を適用するための設定。

    [General]
    UseInclude=1
    
    [IncludeModule]
    puttyjp.exe
    emacs.exe
    mintty.exe

### PuTTY

参考: [Solarizedでターミナルを美しく](http://www.yuuan.net/item/675)

* [Solarize](http://ethanschoonover.com/solarized) で配色設定を変更する。
* フォント設定を Ricty Discord, 10-point にする。
* MacType 適用アプリケーションに puttyjp.exe を追加する。


<!-- Local Variables: -->
<!-- coding: utf-8 -->
<!-- End: -->
