---
layout: post
title: GitHub Pages の Jekyll にバージョンを合わせる
date: 2021-06-26 15:20 +0900
---

GitHub Pages が使用している Jekell とローカルで動かしている Jekell との
バージョンがずれてきたのでメンテナンスを行った。


## Jekyll の依存バージョンの確認

GitHub Pages が依存している Jekyll その他のバージョンは
[GitHub Pages: Dependency versions](https://pages.github.com/versions/)
で確認することができる。

現時点 (2021.06.26) で GitHub Pages が依存している jekyll のバージョンは 3.9.0 
となっていた。

ローカルでインストールされている jekyll のバージョンを確認すると 3.8.5 だった。

```
PS E:\test\blog> jekyll -version
jekyll 3.8.5
```

今のところ、特に問題は生じていないものの、
このまま放置してバージョンのギャップが大きくなると、
移行の際の作業が大変になるかもしれないので更新作業を行うことにした。


## 作業用の Gemfile の作成

今のローカルの Jekyll は Ruby の gem とかいうツールで入れたものだった、が、
各パッケージ(?)のバージョンを管理するには bundler を利用すべきらしい。

Ruby のルールはよく知らないが、bundler を利用すると、
パッケージ間のバージョンの依存関係を調整してインストールしてくれるのだろう。

まずはまっさらな状態の Gemfile のテンプレートを取得するため、
作業フォルダを用意して、`bundle init` を実行する。

```
PS D:\test> bundle init
Writing new Gemfile to D:/test/Gemfile
```

空の Gemfile (といっても初めの方に何やら定義が書かれている) が作成されるので、
Gemfile に Jekyll を使用するための記述を追加する。
この段階では Jekyll のみをインストールし、他のパッケージは指定しない。
指定する Jekyll のバージョンはもちろん 3.9.0 である。

```
gem "jekyll", "~> 3.9.0"
```

はい、意味不明な記号の出現。
今回はバージョン管理をする趣旨なので、記号の意味も確認せざるをえない。

調べたところ、
`~>` は悲観的バージョン演算子 (pessimistic operator) と呼ばれていて、
この場合は 3.9.0 以上の 3.9.x が対象となるようだ。
3.9.x であれば互換性が保たれているとみなせるということだろう。

ここで仮に `~> 3.9.1` を指定すると、
3.9.1 以上の 3.9.x は対象だけれども 3.9.0 は対象外というルールのようだ。
実用的ではあるように見える。
各パッケージのバージョン番号付け規則が厳格であれば。

おそらく Ruby の世界ではこれが機能するくらいにはちゃんとしてるのだろう。
ちゃんとしていることはいいことではある。


## Bundler による作業用 Jekyll のインストール

作成した Gemfile のあるフォルダで `bundle install` を実行すると、
記述内容に従ってパッケージのインストールが行われる。

```
PS D:\test> bundle install
Fetching gem metadata from https://rubygems.org/..........
Resolving dependencies...
Fetching public_suffix 4.0.6
Installing public_suffix 4.0.6
(snip)
Fetching jekyll 3.9.1
Installing jekyll 3.9.1
Bundle complete! 1 Gemfile dependency, 26 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
Post-install message from sass:

Ruby Sass has reached end-of-life and should no longer be used.

* If you use Sass as a command-line tool, we recommend using Dart Sass, the new
  primary implementation: https://sass-lang.com/install

* If you use Sass as a plug-in for a Ruby web framework, we recommend using the
  sassc gem: https://github.com/sass/sassc-ruby#readme

* For more details, please refer to the Sass blog:
  https://sass-lang.com/blog/posts/7828841

```

Jekyll のバージョン指定は `~> 3.9.0` であるが、
実際にインストールされたのは Jekyll 3.9.1 となった。

```
PS D:\test> bundle exec jekyll -version
jekyll 3.9.1
```


## テンプレート用に新規 Jekyll サイトを構築

Jekyll サイトのひな形を取得するため、
先程 Bundler でインストールした Jekyll を使って新規の Jekyll サイトを作成する。

まずは `jekyll new` を使用して my-sitye という名前で jekyll の新規サイトを作成する。
もちろん名前はなんでもよい。

```
PS D:\test> bundle exec jekyll new my-site
Running bundle install in D:/test/my-site...
  Bundler: Using public_suffix 4.0.6
(snip)
  Bundler: Using jekyll 3.9.1
  Bundler: Bundle complete! 1 Gemfile dependency, 26 gems now installed.
  Bundler: Use `bundle info [gemname]` to see where a bundled gem is installed.
New jekyll site installed in D:/test/my-site.
```

`jekyll new` で作成されたフォルダにも Gemfile がある。

```
PS D:\test\my-site> ls -Name
_posts
.gitignore
404.html
about.md
Gemfile
index.md
_config.yml
```

こちらの Gemfile での Jekyll のバージョン指定は `~> 3.9.1` となっていた。

```
PS D:\test\my-site> cat Gemfile | Select-String '^gem "jekyll"'

gem "jekyll", "~> 3.9.1"

```

jekyll のバージョンが 3.9.1 でも別にいいかなと思ったが、
github-pages のバージョン 215 との組み合わせでエラーになるので
[GitHub Pages: Dependency versions](https://pages.github.com/versions/)
で記述されているバージョンに合わせておくのがよさそうだ。

まとめると以下のような感じになる。

```
gem "jekyll", "~> 3.9.0"

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5.1"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", "~> 215", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.15.1"
  gem "jekyll-sitemap", "~> 1.4.0"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?

# kramdown v2 ships without the gfm parser by default. If you're using
# kramdown v1, comment out this line.
gem "kramdown-parser-gfm"
```


作成したフォルダ上で bundle が実行できるように bundle install を実行する。

```
PS D:\test\my-site> bundle install
Fetching gem metadata from https://rubygems.org/..........
Resolving dependencies...
Using public_suffix 4.0.6
(snip)
Fetching wdm 0.1.1
Installing wdm 0.1.1 with native extensions
Bundle complete! 7 Gemfile dependencies, 34 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

既存の Jekyll サイトの環境を更新する場合は、
ここで得られた新規 Jekyll サイトに含まれる
Gemfile その他をマージしていくことになるだろう。

もっと簡単な方法があるかは知らない。
