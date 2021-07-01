---
layout: post
title: Jekyll の GitHub Pages 用 Gemfile 設定を見直す
date: 2021-07-01 09:30 +0900
---
GitHub Pages 用に設定したフォルダの
Gemfile.lock を眺めていたら
gem を個別に指定しなくても依存関係で入ってくるようにみえたので整理した。

## 依存関係でインストールされる

[以前]({{ site.baseurl }}/2021/06/26/start-up-jekyll.html),
[以前2]({{ site.baseurl }}/2021/06/29/jekyll-remote-theme)
GitHub Pages 用の Gemfile の設定について、
使用する Jekyll のプラグインを読み込むように Gemfile に記述する、
プラグインのバージョンを
[GitHub Pages: Dependency versions](https://pages.github.com/versions/)
で確認して合わせる、
とかいうようなことを書いたのだけれども、
生成された Gemfile.lock を確認すると、
github-pages の依存関係で入ってくるので記述が不要そうなことに気づいた。

そもそも、`jekyll new` で生成された Gemfile をみると、
次のような記述があり、
`gem "github-pages"` を指定すれば `gem "jekyll"` の指定も不要だった。

```
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
# gem "github-pages", group: :jekyll_plugins
```

## Gemfile

ということで、整理した Gemfile の全体がこれ。

```
source "https://rubygems.org"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", "~> 215", group: :jekyll_plugins

# If you have any plugins, put them here!
#group :jekyll_plugins do
#  gem "jekyll-feed", "~> 0.6"
#end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.0", :install_if => Gem.win_platform?
```

## 解説

基本的に指定するのは github-pages だけでよい。
jekyll や minima も依存関係でインストールされるので不要。
ただし github-pages のバージョン指定はちゃんとする。

```
gem "github-pages", "~> 215", group: :jekyll_plugins
```

プラグインは github-pages の依存関係で入ってくるし、
依存関係で入ってこないプラグインを指定してもおそらく GitHub Pages では有効にならないので、
ばっさりカット、は気が引けるのでコメントアウトしておく。

```
# If you have any plugins, put them here!
#group :jekyll_plugins do
#  gem "jekyll-feed", "~> 0.6"
#end
```

Windows で Jekyll を動かすための gem はそのまま残しておく。

```
# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.0", :install_if => Gem.win_platform?
```
