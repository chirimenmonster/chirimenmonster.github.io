---
layout: post
title: Ghostscript 9 で CID フォントを使った縦書きをやってみた
date: 2021-08-21 13:45 +0900
---
Ghostscript の縦書きサポートは 7.07 を最後に壊れたままになっていると言われているが、
それは TrueType フォントを使うときの話なのか、
CID フォントを使う場合でもそうなのかについては
よくわからなかったので、
最新の Ghostscript 9.54 で
確認してみた。


## はじめに

Ghostscript の縦書き日本語対応には問題がある。

TeX Wiki Ghostscript 7.07 における
[参考：Ghostscript のバージョンと歴史的経緯](https://texwiki.texjp.org/?Ghostscript%207.07#y494a6b5)
によれば、
かつて gs-cjk プロジェクトにより作成された日本語化・CJK化対応パッチが
Ghostscript 7.07 の時点では本家に統合されていたものの、
その後 (Ghostscript 8 以降) は取り込まれずに、
Ghostscript のCJK対応は不十分なままとなっている、とある。

実際に手元で試してみると、
Ghostscript 7.07 で正しく日本語の縦書きが表示される Postcript ファイルが、
9系列では表示位置がずれるという結果が確認された。
縦書き時のグリフの原点 (グリフの上端中央が想定される)
が横書き時のまま (グリフの左下) になっているような挙動である。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">左が GS 7.07、右が 9.52 の出力、縦書きで位置ずれが発生してるのは、参照点が異なってるっぽい。8.x の頃にあった縦書きグリフ選択の問題は問題なさそう。位置ずれはGS側の問題かフォントに起因するのか、仕様としてはどっちもありで、前者の環境しか想定していない Postscript のコードがダメなのか <a href="https://t.co/yD7OyTm1kB">pic.twitter.com/yD7OyTm1kB</a></p>&mdash; Chirimen (@chirimenspiral) <a href="https://twitter.com/chirimenspiral/status/1403592800722034688?ref_src=twsrc%5Etfw">June 12, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

ただし、ここで使用したのは TrueType フォントである。
本来 Postscript で使用を想定されていたフォントは CID keyed フォントなので、
単に TrueType フォント対応が不十分なだけ、ということも考えられる。
CID keyed フォントだと縦書き時の位置ずれなどの問題は生じないのかもしれない、
ということで確認してみることにした。

CID keyed フォント、だと長くてタイプが面倒なので、
特に誤解を招くような場合でなければ、単に CID フォント、と表現することにする。


## CID フォント

正直なところ、CIDフォントとはどういうものかはよく知らない。
Postscript で日本語などを取り扱う仕組みだと聞いたことがあるくらいで、
直接CIDフォントを取り扱ったことはない。
たぶんプリンタには内蔵されていたのだろう。
それに、現在はCIDフォントは使われていない
([ITmedia: アドビ、CIDフォントのサポート終了へ, 2018.12.26](https://www.itmedia.co.jp/news/articles/1812/26/news084.html))
というような話もある。

[フォントのしくみ (第3回 DTPの勉強会 狩野宏樹)](https://www.iwatafont.co.jp/news/img/about_font.pdf)
によれば、
OpenTypeフォントのうち、
拡張子 .otf となっているものの中にCIDフォントとして使えるものがあるらしい。

フリーのCIDフォントはあまり見かけないが、
[原ノ味フォント](https://github.com/trueroad/HaranoAjiFonts)
を見るとCIDフォントとして使用できるOpenTypeフォントのようなので、
今回は原ノ味フォントを検証に用いることにした。


## 検証環境の準備
### Ghostscript 9 のインストール

Ghostscript 公式の[ダウンロードページ](https://www.ghostscript.com/download/gsdnld.html)の
Ghostscript AGPL Release のところから
Windows 用のインストーラー gs9540w64.exe をダウンロードしてインストールする。
特筆する点は特にない。

デフォルトだと C:\Program Files\gs\gs9.54.0 にインストールされるので、
その前提で以下の記述を行う。

### CIDフォント (OpenTypeフォント) の設定

TeX Wiki: Ghostscript/Windows の
[CID font](https://texwiki.texjp.org/?Ghostscript%2FWindows#dc743646)、
[日本語 OpenType font](https://texwiki.texjp.org/?Ghostscript%2FWindows#j4509a9f)
の記述を参考に設定を行う。


#### フォントファイルの設置

ダウンロードしたCIDフォントのファイルを
C:\Program Files\gs\gs9.54.0\Resource\CIDFont
にコピーし、ファイル名は拡張子がないものに変更する。

HaranoAjiMincho-Regular.otf であれば
HaranoAjiMincho-Regular というようにである。


#### CMap名付きのフォント定義

HaranoAjiMincho-Regular-UniJIS-UTF8-H のように、
CMap名付きでフォントにアクセスするための定義を行う。

定義は次のようになる。
この内容を HaranoAjiMincho-Regular-UniJIS-UTF8-H というファイル名で
C:\Program Files\gs\gs9.54.0\Resource\Font
に設置する。
定義の内容は TeX Live 2021 付属の gs (tlgs) での設定を参考に作成した。

```postscript
%!PS-Adobe-3.0 Resource-Font
%%DocumentNeededResources: UniJIS-UTF8-H (CMap)
%%IncludeResource: UniJIS-UTF8-H (CMap)
%%BeginResource: Font (HaranoAjiMincho-Regular-UniJIS-UTF8-H)
(HaranoAjiMincho-Regular-UniJIS-UTF8-H)
(UniJIS-UTF8-H) /CMap findresource
[(HaranoAjiMincho-Regular) /CIDFont findresource]
composefont
pop
%%EndResource
%%EOF
```

縦書き用は HaranoAjiMincho-Regular-UniJIS-UTF8-V というファイル名で
次の内容とした。
前述のファイルとの違いは、
フォント名末尾の -H が -V となっている部分のみである。 

```postscript
%!PS-Adobe-3.0 Resource-Font
%%DocumentNeededResources: UniJIS-UTF8-V (CMap)
%%IncludeResource: UniJIS-UTF8-V (CMap)
%%BeginResource: Font (HaranoAjiMincho-Regular-UniJIS-UTF8-V)
(HaranoAjiMincho-Regular-UniJIS-UTF8-V)
(UniJIS-UTF8-V) /CMap findresource
[(HaranoAjiMincho-Regular) /CIDFont findresource]
composefont
pop
%%EndResource
%%EOF
```

#### フォントフォルダ読み込みのための追加指定

現在の Ghostscript では環境変数 GS_LIB による読み込みパスの指定は不要とされているが、
追加したCIDフォントを使おうとすると、
下記のようなエラーがでてフォントファイルの読み込みに失敗した。
今回フォントを設置した CIDFont フォルダが
gs のデフォルトの読み込みパスに含まれていないと思われる。

```
Can't find (or can't open) font file %rom%Resource/Font/%rom%Resource/Font/HaranoAjiMincho-Re.
Can't find (or can't open) font file HaranoAjiMincho-Regular-UniJIS-UTF8-H.
Didn't find this font on the system!
Substituting font Courier for HaranoAjiMincho-Regular-UniJIS-UTF8-H.
```

オプション -I を使用して
`-I 'C:\Program Files\gs\gs9.54.0\Resource\CIDFont'`
のようにすれば CIDFont フォルダを gs の読み込みパスに追加できる。


## 縦書き指定時の挙動確認

### Postscript ファイル

動作確認用に、次のようなファイルを UTF-8 で test.ps として作成した。
原ノ味フォントを使用して、横書きと縦書きの出力をそれぞれ行っている。
current point の移動を確認するため、
文字列の描画の前後で current point の位置を赤丸として描画している。

```postscript
%!PS
% current point 描画用のコマンド定義
/DrawCurrentPoint {
    currentrgbcolor
    1 0 0 setrgbcolor
    currentpoint currentpoint 4 0 360 arc fill moveto
    setrgbcolor
} def

% 背景を白で描画
currentrgbcolor 1 setgray
0 0 960 540 rectfill
setrgbcolor

% 横書きの確認
100 360 moveto
DrawCurrentPoint
/HaranoAjiMincho-Regular-UniJIS-UTF8-H findfont 72 scalefont setfont
(原ノ味明朝―Ｈ。) show
DrawCurrentPoint

% 縦書きの確認
100 180 moveto
DrawCurrentPoint
/HaranoAjiMincho-Regular-UniJIS-UTF8-V findfont 72 scalefont setfont
(原ノ味明朝―Ｖ。) show
DrawCurrentPoint

showpage
```

### コマンドラインオプション

gs は次のようにして実行して 960 x 540 の PNG ファイルを作成した。
解像度を 72 dpi (Postscript のデフォルト解像度と同じ) で指定しているので、
ピクセル単位での位置と Postscript での座標が一致する (原点と正負の向きは異なる) ようになっている。

```
& 'C:\Program Files\gs\gs9.54.0\bin\gswin64c.exe' -I 'C:\Program Files\gs\gs9.54.0\Resource\CIDFont' -dBATCH -dNOPAUSE -sDEVICE='pngalpha' -g960x540 -r72 -sOutputFile='result.png' test.ps
```

### 実行結果

実行結果は次のようになった。
上が横書きを指定した場合、
下が縦書きを指定した場合である。

![実行結果](/images/2021-08-21-gs-result.png)

残念ながら、縦書きフォントを指定しても、
横書きのままであった。
ダッシュや句点などに縦書き用のグリフが選択されているので、
全く横書きのままというわけでもない。


## まとめ

CID keyed フォントを使用した場合であれば
Ghostscript の縦書きが使用に耐えうるかどうかを原ノ味フォントを使って試してみた。

結果は、縦書きグリフは選択されるものの、
グリフの原点や文字送りの方向は横書きのままで、
使用できるようなものではなかった
(ちなみに TrueType フォントであれば位置ずれはあるものの縦書きにはなる)。

作成したフォントの定義ファイルが間違っていたのか、
使用したフォントの問題か、
Ghostscript 側の問題であるかの切り分けはできていない。
