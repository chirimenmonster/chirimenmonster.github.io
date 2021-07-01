---
layout: post
title: Windows 用 Emacs のインストール
date: 2016-02-02 20:42:00 +0900
---
Windows 用 Emacs 24.5 をインストールしたときのメモ。

# 手順

[GitHub:chuntaro/NTEmacs64](https://github.com/chuntaro/NTEmacs64)
から
emacs-24.5-IME-patched-generic-cpu.zip
をダウンロードする。

zip ファイルを展開して c:\emacs\emacs-24.5 にコピーする。

c:\emacs\emacs-24.5\bin\runemacs.exe を実行すれば
Emacs が使用できる。
起動しやすいようにタスクバーにピン止めしておく。

~/.emacs.d/init.el の内容

```elisp
;; ツールバー非表示
(tool-bar-mode 0)

;; デフォルトフォント設定
;(add-to-list 'default-frame-alist '(font . "ricty-12"))
(add-to-list 'default-frame-alist '(font . "Ricty Diminished Discord-10"))

;; tramp から plink が使えるように PATH に PuTTY フォルダを追加
;; ここでは PuTTY はユーザーのドキュメントフォルダに置いている
(setenv "PATH"
	(concat
	 (getenv "PATH") ";"
	 (getenv "USERPROFILE") "\\Documents\\PuTTY"))

(require 'tramp)
(setq tramp-default-method "plink")
(setq tramp-shell-prompt-pattern "^[ $]+")
```

# 参考
+ [GitHub:chuntaro/NTEmacs64](https://github.com/chuntaro/NTEmacs64)
+ [Qiita:Emacs のフォント設定について](http://qiita.com/melito/items/238bdf72237290bc6e42)
