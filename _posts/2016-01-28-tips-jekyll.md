---
layout: post
title: GitHub Pages + Jekyll (kramdown) で記述するときの TIPS
date: 2016-01-28 15:20:00 +0900
---
Jekyll を使って GitHub Pages にポストするようになって気づいたことのまとめ。

## はじめに

Jekyll で GitHub Pages に載せる文書を書くようになったのだけれど、
書き方 (文法) についてはまだまだわからないことが多く、
ネットで調べながら書いている。

でも、ネットで見つかった事例が手元では動作しないことも多い。

これはバージョンの新旧や、マークダウンを処理するエンジンの違いなどが
影響しているらしい。

なので、これまでにちょっとハマった TIPS をここに記していく。


## 環境について

Windows PC の Jekyll でプレビューして Git で GitHub に上げる使い方を想定している。

マークダウンのエンジンは Kramdown で、コードハイライトは Pygments だ。
ただし、プレビュー時のコードハイライトは Rouge で処理してる。

## TIPS

### コメントアウト

`{% raw %}{% comment %}{% endraw %}` と `{% raw %}{% endcomment %}{% endraw %}`
で囲まれた部分がコメントアウトされる。
HTML に出力されない。

{% highlight text %}
{% raw %}{% comment %}
囲まれた部分がコメントとして無視される
{% commentout %}{% endraw %}
{% endhighlight %}


### Liquid のエスケープ

参考: [stackoverflow: How to escape liquid template tags?](http://stackoverflow.com/questions/3426182/how-to-escape-liquid-template-tags)

`{% raw %}{% raw %}{% endraw %}` と `{{ "{% endraw " }}%}` で囲む。
または `{% raw %}{{ "何か" }}{% endraw %}` で Liquid として解釈されないように分割する。

例1:
{% highlight text %}
{% raw %}{% raw %}{% something %}{% endraw %}{{ "{% endraw " }}%}
{% endhighlight %}

例2:
{% highlight text %}
{% raw %}{{ "{% something " }}%}{% endraw %}
{% endhighlight %}

2番目の例では `{% raw %}{% something %}{% endraw %}` を
`{% raw %}"{% somthing "{% endraw %}` と `{% raw %}"%}"{% endraw %}`
に分割し、
前半を `{% raw %}{{ "何か" }}{% endraw %}` の中に入れている。


### コードブロックのハイライト

`{% raw %}{% highlight <lang> [linenos] %}{% endraw %}` と
`{% raw %}{% endhighlight %}{% endraw %}`
で囲む。
`<lang>` はコードブロックの言語で必須、
`[linenos]` は行番号を付けるかどうかのオプション。

`~~~` で囲む指定方法もある。

_config.yml に以下の指定をするとコードブロックの区切りに
```` ``` ```` が使えるようになる、との記述を見たが、
試した限りでは動かなかった。

{% highlight yaml %}
kramdown:
  input: GFM
{% endhighlight %}


### バッククォートが含まれるインラインコード

参考: [Qiita: Markdown でバッククオートを含むインラインコードを書くには](http://qiita.com/uasi/items/251f4e66ceb95c043b3d)

中に現れるバッククォートよりも多く連続するバッククォートで囲む。