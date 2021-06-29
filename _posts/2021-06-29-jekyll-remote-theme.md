---
layout: post
title: Jekyll のテーマを GitHub Pages に適用する
date: 2021-06-29 19:00 +0900
---
ここ数日で作成した Jekyll のテーマ関連の設定だが、
いざ GitHub に up してみると動作しなかった。
確認すると `remote_theme` として設定しないといけなかったようだ。

## GitHub Pages で remote_theme を設定する

GitHub Pages で Bulma Clean Theme を使用するには、
`_config.yml` で設定される `theme` の代わりに
`remote_theme` を設定すればよい。

設定するのはテーマの GitHub におけるリポジトリの名称になる。
Bulma Clean Theme であれば `chrisrhymes/bulma-clean-theme` だ。

```
remote_theme: chrisrhymes/bulma-clean-theme
```

## ローカルの Jekyll で remote_theme を有効にする

GitHub Pages ではすでに `remote_theme` が有効になっているので上記の設定だけで動作するが、
ローカルの Jekyll には追加の設定が必要となる。

### Gemfile で jekyll-remote-theme をインストールする

Gemfile に `gem "jekyll-remote-theme"` を記述して
jekyll-remote-theme がインストールされるようにする。
記述場所は `jekyll_plugins` のあたりでよいだろう。

```
gem "github-pages", "~>215", group: :jekyll_plugins

group :jekyll_plugins do
  (snip)
  gem "jekyll-remote-theme", "~> 0.4.3"
end
```

追加したら `bundle install` を実行する。

### _config.yml に jekyll-remote-theme プラグインの追加を行う

`_config.yml` の `plugins` のところに `jekyll-remote-theme` を追加する。

```
plugins:
  - jekyll-remote-theme
```

### _config.yml に remote_theme の設定を行う

```
remote_theme: chrisrhymes/bulma-clean-theme
```

以上の設定で、ローカルの Jekyll でも remote_theme が使えるようになる。

あとは、いつも通りローカルで確認して GitHub に up すれば、
指定したテーマが適用された GitHub Pages が確認できるはずだ。
