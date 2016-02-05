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

マークダウンのエンジンは Kramdown で、コードハイライトは Rouge だ
(2016/02/01 に GitHub Pages で使用する Jekyll が 3.0 になったのにともなって
Pygments から Rouge に変更された)。

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


### PHP のシンタックスハイライト

先頭行が `<?php` で始まっている必要がある。

シンタックスハイライトのエンジンが pygments だと、
startinline オプションを指定して
`<?php` で開始しないコードブロックを PHP として処理させることが
できたようだが、rouge だと動作しない。

{% highlight liquid %}{% raw %}
{% highlight php startinline %}
echo $_SERVER['HTTP_USER_AGENT'];
{% endhighlight %}
{% endraw %}{% endhighlight %}


### 時刻

記事のタイムゾーンは OS のタイムゾーン、
例えば環境変数 `TZ` が指定されているものが使用される。

Jekyll のドキュメントでは
_config.yml で `timezone: ` を指定すると
OS のタイムゾーンの代わりに _config.yml で記述したものが使用されるとあったが、
手元ではそうならなかった。

`timezone: ` を指定すると内容にかかわらず UTF になってしまう。

以下は TZ=Asia/Tokyo の状態で
`date: 2016-02-04 09:03:00 +0900` を date_to_xmlschema で出力した結果。

_config.yml で何も指定しない場合。

```
2016-02-04T09:03:00+09:00
```

_config.yml で `timezone: Asia/Tokyo` を指定した場合。

```
2016-02-04T00:03:00+00:00
```


