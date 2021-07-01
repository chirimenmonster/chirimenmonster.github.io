---
layout: post
title: Jekyll の Bulma Clean Theme に MathJax を組み込む
mathjax: true
date: 2021-06-29 17:00 +0900
---
Jekyll に MathJax のサポートはあるものの、
なぜか MathJax のスクリプトを読み込む部分は自分で用意しないといけないようになっている。
せめてプラグインで読み込めるようになっていればいいのにそうはなっていない。

大抵の場合、テーマのファイルを変更して埋め込むようなことになるので、
Bulma Clean Theme でのケースについて記録を残す。


## MathJax の読み込み

[MathJax の公式ドキュメント](http://docs.mathjax.org/en/latest/web/start.html)に従い、
次のようなコードが html に埋め込まれるようにする。

```html
<script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>
```


## Bulma Clean Theme のコード修正

[Bulma Clean Theme の GutHub](https://github.com/chrisrhymes/bulma-clean-theme)
から `_includes/head-scripts.html` をダウンロードし、
サイトの `_includes` フォルダ (なければ作成する) にコピーする。

`head-scripts.html` の中身は html コメント 1 行のみである。
実質何もしない。

```html
<!-- head scripts -->
```

これを次のように書き換える。
対象ページの Front Matter に `mathjax: true` があれば
`_include/mathjax-v3.html` の内容を埋め込む、
という記述である。
常に MathJax が有効だと使いづらい面もあるかと思うので、
指定したページでのみ有効となるようにしている。

```html
<!-- head scripts -->
{% raw %}{% if page.mathjax %}{% include mathjax-v3.html %}{% endif %}{% endraw %}
```

`_include/mathjax-v3.html` を次のような内容で作成しておく。
前半は MathJax の設定、後半は CDN からの MathJax コードの読み込みである。

```html
<!-- Begin mathjax-v3 -->
<script>
    MathJax = {
        tex: {
            inlineMath: [['$','$'], ['\\(','\\)']],
            processEscapes: true,
            tags: 'ams'
        }
    };    
</script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js"></script>
<!-- End mathjax-v3 -->
```

## 表示サンプル


次のようなコードを本文に記述すると
```
$$ E = m c^2 \tag{1}$$
```

このような表示が得られる。

$$ E = m c^2 \tag{1}$$


また、

```
行内数式では $E = m c^2$ のように表示される。
```

行内数式では $E = m c^2$ のように表示される。
