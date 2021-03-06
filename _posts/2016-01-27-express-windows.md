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
* [Ricty](https://github.com/yascentur/Ricty) ([Ricty Diminished](https://github.com/yascentur/RictyDiminished))

## 設定

### Firefox

#### URL のトリミングを無効に

|パラメータ|値|
|:---|:---|
|browser.urlbar.trimURLs|false|


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
|gfx.direct2d.disabled|false|
|gfx.font_rendering.directwrite.enabled|true|
|gfx.font_rendering.cleartype_params.gamma|1200|
|gfx.font_rendering.cleartype_params.enhanced_contrast|80|
|gfx.font_rendering.cleartype_params.cleartype_level|100|
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
* フォント設定を Ricty Discord, 10-point (または Ricty Diminished Discord, 10-point) にする。
* MacType 適用アプリケーションに puttyjp.exe を追加する。 (または PuTTY 側で「フォントの品質」に ClearType を設定)

## MSYS2 (mintty)

* ダウンロードとインストール ([MSYS2 installer](https://msys2.github.io/))
* Solarize に合わせた配色設定 (参考: [Color settings for the Cygwin mintty terminal](https://github.com/mavnn/mintty-colors-solarized))
* proxy 設定 (参考: [msys2 + pacmanをproxy環境で使う](http://nantonaku-shiawase.hatenablog.com/entry/2014/10/11/163342))
* `update-core` と `pacman -Su` で最新版に更新。ただし `update-core` の実行後、msys2 のウィンドウを閉じて再度 msys2 を実行する必要がある (参考: [Qiita:MSYS2における正しいパッケージの更新方法](http://qiita.com/k-takata/items/373ec7f23d5d7541f982), [MSYS2 installer](https://msys2.github.io/))。
* 開発環境が必要な場合、`pacman -S base-devel`, `pacman -S mingw-w64-x86_64-toolchain` で必要なパッケージをインストール。


<!-- Local Variables: -->
<!-- coding: utf-8 -->
<!-- End: -->
