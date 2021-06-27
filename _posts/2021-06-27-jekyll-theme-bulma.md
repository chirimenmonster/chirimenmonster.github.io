---
layout: post
title: Jekyll のテーマを変えてみる
date: 2021-06-27 09:00 + 0900
---

Jekyll のデフォルトテーマは minima となっているが、
他のテーマに変更することもできる。
公開されていれるテーマはたくさんあるが、
ここでは bulma-clean-theme に変更してみた。

## Jekyll の初期テーマ (minima)

`jekyll new` で新規 Jekyll を作成したとき、
テーマには minima が設定されており、
サイトの見た目は次のようになっている。

![Jekyllの初期テーマ (minima)]({{site.baseurl}}/images/2021-06-27-jekyll-minima-init.png)

テーマはサイトの `_config.yml` で設定されている。
ファイルを見ると次のような行が含まれていることが確認できるだろう。

```
theme: minima
```

## Jekyll のテーマを探す

Jekyll の公式ドキュメントには下記のギャラリーサイトが紹介されているので、
この中から GutHub Page で使用可能で、かつ、
目的にあってそうなテーマを適当に選べばよい。

+ [GitHub.com #jekyll-theme repos](https://github.com/topics/jekyll-theme)
+ [jamstackthemes.dev](https://jamstackthemes.dev/ssg/jekyll/)
+ [jekyllthemes.org](http://jekyllthemes.org/)
+ [jekyllthemes.io](https://jekyllthemes.io/)
+ [jekyll-themes.com](https://jekyll-themes.com/)


今回は
[Bulma Clean Theme](https://jekyllthemes.io/theme/bulma)
を選択した。


## Bulma Clean Theme のインストール

[Bulma Clean Theme のドキュメント](https://github.com/chrisrhymes/bulma-clean-theme/)
に従ってインストールを行う。

といっても、Gemfile に
`gem "bulma-clean-theme"`
の行を追加して bundle を実行するだけだ。

Gemfile に bulma-clean-theme を追加する。

```
gem "bulma-clean-theme"
```

bundle install を実行

```
PS D:\test\my-site> bundle install
Using public_suffix 4.0.6
(snip)
Using bulma-clean-theme 0.11.2
(snip)
Bundle complete! 9 Gemfile dependencies, 37 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

## _conffig.yml を編集して Jekyll のテーマを変更する

_config.yml で theme に bulma-clean-theme を指定する。
元の minima は削除してもよいのだが、
コメントアウトしておこう。

```
#theme: minima
theme: bulma-clean-theme
```

## 変更後の Jekyll を実行してみる

_config.yml で theme を変更したあと、
何も考えないで Jekyll を実行してみると、
次のように、
`home` というレイアウトがないというエラーになった。
`home` は Jekyll の初期 index.md で指定されているレイアウトであるが、 
bulma-clean-theme では `home` が提供されていないようだ。

```
PS D:\test\my-site> bundle exec jekyll serve
Configuration file: D:/test/my-site/_config.yml
            Source: D:/test/my-site
       Destination: D:/test/my-site/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
     Build Warning: Layout 'home' requested in index.md does not exist.
                    done in 1.372 seconds.
 Auto-regeneration: enabled for 'D:/test/my-site'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

## layout の指定を変更して jekyll を実行する

`page` は存在するようなので
`index.md` の layput 指定を `page` に変更する。
他にも Bulma Clean Theme の index.md を見ながら
`title`, `subtitle`, `show_sidebar` も設定しておく。

```
---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

#layout: home
layout: page
title: Bulma Clean Theme
subtitle: This is the demo site for Bulma Clean Theme
show_sidebar: true
---
```

Jekyll を 最後実行すると、
次のようなページが得られた。

![Jekyllの bulma-clean-theme]({{site.baseurl}}/images/2021-06-27-jekyll-bulma-init.png)

あとは
[Bulma Clean Theme のドキュメント](https://github.com/chrisrhymes/bulma-clean-theme/)
を参考に設定などをしていく。
