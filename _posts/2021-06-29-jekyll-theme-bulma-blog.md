---
layout: post
title: Jekyll テーマ bulma-clean-theme をブログ用に調整する
date: 2021-06-29 14:30 +0900
---
Jekyll のテーマを Bulma Clean Theme に変更してみた
[前回]({{site.baseurl}}/2021/06/27/jekyll-theme-bulma.html)
の続きで、
ブログ用に設定を調整していく。


## ブログ用のレイアウト

Bulma Clean Theme にはブログ用のレイアウトとして
`blog` が用意されている。

このレイアウトは Jekyll の Pagination (ページ分け)
([公式ドキュメント](https://jekyllrb.com/docs/pagination/),
[日本語訳](http://jekyllrb-ja.github.io/docs/pagination/))
を使用する前提で設計されているので、
まずは素直に従っておく。

レイアウトスタイルに不満がでてきたら、
カスタマイズするなり、他のテーマを探したりすればよいだろう。


## _config.yml での設定

_config.yml にページ分けをするための設定を行う。

必要なのは1ページにいくつの記事を表示するかの設定と、
ページ分けされたファイルの出力先の設定である。

ここでは次のようにした。

```yml
paginatite: 5
paginate_path: "/./page:num/"
```

`paginate: 5`
は _post に置かれた記事を1ページに5つまで表示するという指定である。

`paginate_path: "/./page:num/"`
はページ分けしたファイルの出力先の設定である。
ここでは、サイトのルートディレクトリにブログのインデックスページを置くようにした。
`:num` の部分は実際のページの数字に置き換えられる。
2ページ目であれば `/page2/` というようにである。

設定を `"/page:num/"` でなく、`"/./page:num/"` としているのは
後述するが Bulma Clean Theme の不具合に対する回避策である。
ブログを置くパスが `"/blog/page:num/"` にようにサブディレクトリになっているのであれば、
回避策は不要で単に `"/blog/page:num/"` と指定するのでよい。


## index.html

Jekyll のドキュメントに書かれているように、
ページ分けするファイルは
`index.md` などではなく、
`index.html` でなければならない。

そのため、次のように記述したファイルを
`index.html` という名称でサイトのフォルダのトップに置く。
元からある `index.md` は削除しておいた方がよいだろう。


```markdown
---
layout: blog
title: Bulma Clean Theme
subtitle: This is the demo site for Bulma Clean Theme
show_sidebar: true
---
```

`layout: blog` で指定した Bulma Clean Theme のレイアウト `blog` には、
ページ分けされた記事をレンダリングするためのコードが記述されている。

`show_sidebar: true` は、
ページのサイドバーとして最近の記事を表示するための設定で、
ページ分けの機能とは独立している。


## レンダリング結果

ページ分けの様子が分かりやすいように、
記事を6個にコピーしてレンダリングを行った。


![blog]({{site.baseurl}}/images/2021-06-29-jekyll-bulma-blog.png)
![blog]({{site.baseurl}}/images/2021-06-29-jekyll-bulma-blog-2.png)


## pagenate_path の不具合

Bulma Clean Theme の `pagenate_path` に `/page:num/` を指定すると、
1 ページ目へのリンクボタンが機能しなくなってしまう。

bulma-clean-theme の `_includes/pagination.html` を見ると次のようになっており、
`pagenate_path` が `/page:num/` だと `href` に設定するパスの値が空になる。

```
{% raw %}{% elsif page == 1 %}
<li><a href="{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | split: '/' | pop | join: '/' }}" class="pagination-link">{{ page }}</a></li>
{% else %}{% endraw %}
```

`pagenate_path` が `/blog/page:num/` の場合には、
ページ 1 へのリンクが `/blog`, ページ 2 へのリンクが `/blog/page2` のように設定されるので、
単にパスの末尾の `/` が欠落しているのだと思われる。

根本的には `_includes/pagination.html` を修正すべきではあろうが、
`_includes/pagination.html`
を丸ごとコピーして修正したもので置き換えるといった作業が必要になってくる。
一方、`pagenate_path` を `/./page:num/` と設定すれば、
ページ 1 が `/.`, ページ 2 が  `/./page2` のようになってしまうものの、
ナビゲーション自体は機能し、こちらの方が記述量は少なくてすむ。

