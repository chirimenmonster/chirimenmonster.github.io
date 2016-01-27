---
layout: post
title: GitHub Pages + Jekyll で作成する文書に TeX 数式を埋める
date: 2016-01-27 13:45:00 +0900
use_math: true
---
[Jekyll](https://jekyllrb.com/) で [GitHub Pages](https://pages.github.com/)
に公開するドキュメントに数式を入れる話。

## はじめに

[GitHub Pages](https://pages.github.com/) と
[Jekyll](https://jekyllrb.com/) の組み合わせは
最初に環境を構築するところがちょっと面倒なんだけれど、
そこを乗り越えてしまえばあとのドキュメント作成や管理がずっと楽になる。
Git に慣れてさえいれば。

聞くところによれば、TeX 形式で記述した数式も文書に埋め込めるらしい。
画像じゃなく。へー。

ということでやってみました。

ちなみにこんな感じで埋め込める。

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$


## 準備作業

ネットで検索したらプラグインとかをインストールしろとか書いてる文書もあったけど、
必要なかった。
たぶん文書が古いのだろう。
ここの文書には日付を書いておくので、
参照する人は古い情報でないか気をつけてほしい。


### 数式表示のしくみ

作業の前に、どんなしくみで数式が表示されておくか簡単に書いておく。

数式の表示には [MathJax](https://www.mathjax.org/)
([ウィキペディア:MathJax](https://ja.wikipedia.org/wiki/MathJax))
というのが使われる。
Javascript のツールだ。

数式を表示するには
MathJax が HTML ドキュメントに埋め込まれた数式 (LaTeX で記述されている) を
うまく認識できるようにしてやればいい。

ここのところは Jekyll 標準のマークダウンプロセッサであるところの
Kramdown であれば
[すでに対応済み](http://jekyllrb.com/docs/extras/#math-support)
なのでほとんど何もしなくていい。

Kramdown 以外のを使ってる人は頑張ってなんとかしよう。


### MathJax の読み込み

[さっきのリンク](http://jekyllrb.com/docs/extras/#math-support)
にあるように以下のコードが HTML のどっかに埋め込まれるようにすればよい。

    <script src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

よいのだけれど、GitHub Pages に https で接続したときにブラウザがエラーになる。
ページが https なのに http で MathJax を読みにいくからだ。

そこで以下のようにする。
プロトコル部分 ```http:``` を削除すれば
HTML ドキュメントを読み込んだときと同じプロトコルで MathJax を読みにいく。
幸い cdn.mathjax.org は https でもアクセスできる。

    <script src="//cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

このコードを新規に作成した _includes/math.html に記述しておいて、
_include/head.html からインクルードさせる。
以下は _includes/head.html の抜粋で、4行目の ```{% raw %}{% if page.use_math == true %}{% endraw %}``` と ```{% raw %}{% endif %}{% endraw %}``` で
囲まれた部分がそうだ。

      {% raw %}<link rel="stylesheet" href="{{ "/css/main.css" | prepend: site.baseurl }}">
      <link rel="canonical" href="{{ page.url | replace:'index.html','' | prepend: site.baseurl | prepend: site.url }}">
      <link rel="alternate" type="application/rss+xml" title="{{ site.title }}" href="{{ "/feed.xml" | prepend: site.baseurl | prepend: site.url }}">
      {% if page.use_math == true %}{% include math.html %}{% endif %}
    </head>{% endraw %}

これでドキュメントの Front-Matter に ```use_math: true``` を書いたときだけ
MathJax が読み込まれる。


<!-- Local Variables: -->
<!-- coding: utf-8 -->
<!-- End: -->
