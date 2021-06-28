---
layout: post
title: MSYS2 のアップデート
date: 2016-02-03 10:50:00 +0900
---
MSYS2 のアップデートをしたときのメモ

## 手順

`update-core` コマンドを実行する。

```shell
$ update-core
```

更新があったら MSYS2 を一旦再起動する (重要)。
終了するときには Alt-F4 あるいはクローズボタンを使用する。

`pacman -Su` を実行する。

```shell
$ pacman -Su
```


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

### 64bit 版 Windows の場合

[MSYS2 installer](https://msys2.github.io/)
から 64bit 版 msys2 インストーラ
msys2-x86_64-20150916.exe をダウンロードして実行する。

```sh
$ update-core
(msys2を一旦終了して再実行)
$ pacman -Su
$ pacman -S base-devel
$ pacman -S mingw-w64-x86_64
$ pacman -S mingw-w64-i686
$ pacman -S mingw-w64-x86_64-toolchain
$ pacman -S mingw-w64-i686-toolchain
```

### 32bit 版 Windows の場合

[MSYS2 installer](https://msys2.github.io/)
から 32bit 版 msys2 インストーラ
msys2-i686-20150916.exe をダウンロードして実行する。

```sh
$ update-core
(msys2を一旦終了して再実行)
$ pacman -Su
$ pacman -S base-devel
$ pacman -S mingw-w64-i686
$ pacman -S mingw-w64-i686-toolchain
```


特にエラーとかなくインストールできたっぽい。

### 端末 (mintty) の設定。

|項目|値|
|:---|:---|
|font|Ricty Discard, 10pt|
|locale|ja_JP|
|character set|UTF-8|
|foreground colour| (101, 123, 131) |
|background colour| (253, 246, 227) |
|cursor colour| (238, 232, 213) |
|cursor|Block|
|cursor blinking|なし|


## 参考
+ [Qiita:MSYS2における正しいパッケージの更新方法](http://qiita.com/k-takata/items/373ec7f23d5d7541f982)
+ [Qiita:MSYS2でパッケージのダウンロードに失敗するときの対処](http://qiita.com/k-takata/items/fcb2f1f9ca564fd78597)
+ [MSYS2 installer](https://msys2.github.io/)
