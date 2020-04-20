---
layout: post
title: VSCode の TeX 環境構築
date: 2020-04-20 17:00 +0900
---
Visual Studio Code (VSCode) で TeX を利用するための環境構築を行った。

## はじめに

Windows で TeX を使って日本語の文書を作成するための環境構築を行った。
自分は最近では TeXLive 付属の TeXworks editor というツールを使い、
モード upLaTeX (ptex2pdf) で文書作成を行っていた。

コンパイルに使用するのは upLaTeX で TeX ソースの文字コードは utf8 となっている。
文書中には \includegraphics を使用して図を貼り付けていて
日本語ファイル名が含まれている。
TeX ソースのファイル名も日本語が使われている。


## VSCode 拡張機能 LaTeX WorkShop の導入

VSCode の拡張機能としては LaTeX Workshop が定番らしいので、
素直に LaTeX Workshop を導入することにした。

LaTeX Workshop は VSCode の拡張機能のメニューからインストールできる。


## LaTeX Workshop に関連する設定ファイル

LaTeX Workshop の環境構築に関係する設定ファイルは2つあるようだ。

* setting.json ... VSCode 環境全体に共通の設定
* .latexmkrc ... コンパイル対象文書に固有の設定

setting.json は VSCode 本体の設定ファイルであって、
コマンドパレットの Preferences: Open Settings (JSON) で編集する。

VSCode の流儀にまだ慣れていなくて、
拡張機能の設定項目が VSCode 本体の設定ファイルに記述するというのには若干不安があるが
（拡張機能が増えてきたら settings.json がとんでもなく肥大したりしないのだろうか）、
フォーマットとしての JSON には馴染みがあるし、
皆やっているのだからそういうものなのだろう。

もう一つの .latexmkrc は latexmk というツールが使用する設定ファイルである。
latexmk は TeXLive に同梱されているコマンドで、
TeX 文書のコンパイルから PDF 化までの一連の作業を自動化するツールである。

コマンドラインから latexmk を実行したときに、
意図通りに文書がコンパイルされるように設定を記述しておけばいいようだ。


## 設定の方針

LaTeX Workshop のデフォルトでは、Build LaTeX project を実行すると latexmk を使用して TeX のコンパイルが実行される。
latexmk は TeXLive に同梱のコマンドであり、コマンドラインから実行できる。

Build LaTeX project ツリーの下には複数の Recipe: から始まるコマンドが列挙されていて、
デフォルトではそのうちの先頭のコマンド (Recipe) が実行される（設定で、最後に実行した Recipe を実行するようにもできる）。

デフォルトの Recipe: latexmk は日本語用の upLaTeX を実行するように設定されていないので、
日本語の TeX ソースはコンパイルに失敗する。

## .latexmkrc の設定

まず、コマンドラインから latexmk で TeX 文書をコンパイルできるようにしておく。

.latexmkrc は perl のプログラムで、
変数を設定する以外も記述できるようだ。

```perl
#! /usr/bin/perl

$pdf_mode = 3;

$latex = 'uplatex -synctex=1 -halt-on-error -file-line-error %O %S';
$max_repeat = 5;

$bibtex = 'upbibtex';
$dvipdf = 'dvipdfmx %O -o %D %S';
$makeindex = 'mendex %O -o %D %S';
```

1行目の `#!` で始まる行は不要だが、記述しておくと VSCode が読み込んだときに Perl モードにしてくれる。

`$pdf_mode` は pdf ファイルを作成する方法を指定するもので、
0 から 5 までの値が指定できる。
0 は pdf ファイルを作成せず、
3 を指定した場合は dvi ファイルから後述の `$dvipdf` で指定したコマンドを使用して pdf ファイルを作成する。
日本語TeXを使用する場合はたいてい dvipdfmx を使用するので 3 を指定する。

詳しくは付属のドキュメントか、[latexmk のページ](http://personal.psu.edu/jcc8/software/latexmk/) から参照できるオンラインドキュメントで確認できる。

`$latex` は LaTeX を実行するコマンドの指定。
ここでは upLaTeX を使用するので、"uplatex %O %S" のように記述する。
固定的に指定する追加のオプションもここに一緒に記述しておく。
`%O` や `%S` は後述するプレースホルダーで、
`%O` はオプション、`%S` はソースファイルである。

`$dvipdf` は dvi から pdf ファイルを作成するコマンドの指定である。
`%O` はオプション、`%D` は出力ファイル、`%S` はソースファイルである。
`%O`, `%D`, `%S` は latexmk が状況に応じて生成する。
つまり、もとの TeX ソースが foo.tex の場合、
`$dvipdf` 内では `%S` は foo.dvi, `%D` は foo.pdf が設定される。

`%O` や `%S`、`%D` はプレースホルダーで、
文脈に応じて latexmk が生成するものもある。
`%O` はオプション、
`%S` はソースファイル、
`%D` は出力するファイル名。
これらは latexmk によって生成される。


## settings.json の設定

VSCode のコマンドパレットから setting.json の編集モードに入る。

コマンドパレットは VSCode のメニューの「表示」→「コマンドパレット...」、
または Control+Shift+P で呼び出せる。
そして呼び出したコマンドパレットで Preferences: Open Settings (JSON) を選択する。

設定は2箇所あって、
一箇所は recipes で LaTeX Workshop のメニューに対応したコンパイル手順の定義、
もう一箇所は tools で recips で定義した各手順で実行するコマンドの定義である。


### tools (latex-workshop.latex.tools)

setting.json の編集で、
キー `latex-workshop.latex.tools` を挿入しようとすると
キーの候補が表示され、選択すると現在の設定がコピーされる。
それを元に追加、修正する。

latex-workshop.latex.tools は JSON の配列になっていて、
recipes から実行するコマンドの定義されている。
順序に意味はなさそうだ。

コマンドの定義は JSON のオブジェクトになっていて、
実行するコマンド、引数、環境を指定する。

```json
    "latex-workshop.latex.tools": [
        {
            "name": "latexmk_upLaTeX",
            "command": "latexmk",
            "args": [
                "-f",
                "-gg",
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-outdir=%OUTDIR%",
                "%DOC%"
            ],
            "env": {}
        }
    ]
```

`name` は recipes から参照される名前、
`command` は実行するコマンド名、
`args` はコマンドの引数（配列）、
`env` は環境変数（オブジェクト）である。

### recipes (latex-workshop.latex.recipes)

setting.json の編集で、
キー `latex-workshop.latex.recipes` を挿入しようとすると
キーの候補が表示され、選択すると現在の設定がコピーされる。
それを元に追加、修正する。

latex-workshop.latex.recipes は JSON の配列になっていて、
LaTeX workshop に表示されるメニューの順序に対応する。
特に、最初のエントリは、デフォルトとして扱われる。

コマンドのメニューエントリの定義で、
tools で設定した定義を呼び出す。複数のツールを指定することができ、記述した順に実行される。

```json
    "latex-workshop.latex.recipes": [
        {
            "name": "latexmk (upLaTeX)",
            "tools": [
                "latexmk_upLaTeX"
            ]
        }
    ]
```

`name` はメニューの表示名、
`tools` は実行するツールの配列である。


## 注意点

### 日本語ファイル名

現状、Windows の latexmk では日本語ファイル名を完全には正しく取り扱うことはできず、
Shift_JIS (codepage 932) で表現した場合に文字コード 0x5c ('\\') が含まれるようなパターンであると失敗する。
これは latexmk が Windows 用の Perl で記述されていて、コマンド引数の文字コードの取り扱いが適切でないために生じている。

upLaTeX 本体や、dvipdfmx などでは日本語ファイル名を扱っても問題なさそうである。

