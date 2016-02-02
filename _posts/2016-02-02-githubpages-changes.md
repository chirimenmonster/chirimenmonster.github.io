---
layout: post
title: GitHub Pages の Jekyll が更新された
date: 2016-02-02 11:00:00 +0900
---
GitHub Pages の Jekyll が 3.0 に更新された模様

## 概要

参考: [GitHub Pages now faster and simpler with Jekyll 3.0](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0)

GitHub Pages の Jekyll が 3.0 になって、
いろいろ細かいところで変わったみたい。

highlight が Rouge に変更されたようなので、
_config.yml での指定を書き換え。

{% highlight yaml %}
highlighter: rouge
{% endhighlight %}

あとで詳しく見ていくつもり。
