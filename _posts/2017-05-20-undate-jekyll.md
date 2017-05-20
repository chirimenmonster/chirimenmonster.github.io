---
layout: post
title: Jekyll の更新
date: 2017-05-20 11:00 +0900
---
Jekyll を更新 (Rubyも) したときのメモ

## Ruby の更新
### Ruby 本体
[RubyInstaller](https://rubyinstaller.org/) から Ruby 2.3.3 (x64) をダウンロードしてインストーラを実行する。
指定するオプションは次の2つ。
+ Rubyの実行ファイルへ環境変数PATHを設定する
+ .rbと.rbwファイルをRubyに関連付ける
にチェックを入れてインストール

### DEVEROMENT KIT の再設定
DEVELOPMENT KIT は前回のときすでにインストールしてたので初期設定のみ行う

```
cd C:\RubyDevKit
ruby dk.rb init
ruby dk.rb install
```

## Jekyll の更新
### bundler のインストールと jekyll の更新
Gemfile を含む Jekyll 環境は設定済みなので、新しくインストールした Ruby 用に bundler をインストール。
あとは bundler がやってくれる。

```
gem install bundler
bundle install
```

### エラーの修正
これだけだと `jekyll serve` でドキュメントに更新があったときにエラーになる。
エラーの解決にはいくつか追加の作業が必要。

参考： [http://blog.johannesmp.com/2017/02/13/fixing-jekyll-serve-on-windows/](http://blog.johannesmp.com/2017/02/13/fixing-jekyll-serve-on-windows/)

#### No repo name found.

```
Error: No repo name found. Specify using PAGES_REPO_NWO environment variables, 'repository' in your configuration, or set up an 'origin' git remote pointing to your github.com repository.
```

GitHub のリポジトリの位置を追加で指定する必要がある。

`_config.yml` の `repository` に `<USERNAME>/<REPOSTIROY_NAME>` を設定する。

#### certificate verify failed

```
Error: SSL_connect returned=1 errno=0 state=error: certificate verify failed
```

Ruby に SSL 証明書が入ってないため SSL 接続時の証明書の検証に失敗する。
`cacert.pem` を [http://curl.haxx.se/ca/cacert.pem](http://curl.haxx.se/ca/cacert.pem) からダウンロードして、
`C:\RubyDevKit\SSLCert` などに設置。
環境変数 `SSL_CERT_FILE` で指定する

#### No GitHub API authentication could be found

```
GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
```

GitHub API 用の認証が必要。
GitHub で生成したアクセストークンを環境変数 `JEKYLL_GITHUB_TOKEN` に設定する。
アクセストークンは [https://github.com/settings/tokens](https://github.com/settings/tokens) で取得できる。
トークンの名前は管理用で管理しやすいもの、たとえば `JEKYLL_GITHUB_TOKEN_PCNAME` などをつける。
トークンのスコープは repo にチェックを入れて生成する。

