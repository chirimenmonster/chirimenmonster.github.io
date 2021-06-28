---
layout: post
title: GitHub Pages の jekyll で sitemap を生成してみる
date: 2016-02-16 09:40:00 +0900
---
GitHub Pages で sitemap を生成させる手順。

## 作業内容

ローカル環境に jekyll-sitemap をインストールする。
手元で確認するために必要。

```sh
$ gem install jekyll-sitemap
```

_config.yml に jekyll-sitemap の記述を追加する。

```yml
gems:
  - jekyll-sitemap
```

## 参考
+ [Jekyll Sitemap Generator Plugin](https://github.com/jekyll/jekyll-sitemap)
+ [Sitemaps for GitHub Pages](https://help.github.com/articles/sitemaps-for-github-pages/)
