---
layout: post
title: MediaWiki でページがないときの表示
date: 2016-05-09 13:05:00 +0900
---
MediaWiki で存在しないページを指定したときの表示をカスタマイズ

## 概要

MediaWiki で存在しないページを表示しようとすると、
「現在このページには内容がありません」
のメッセージとともに検索や新規ページの作成のためのリンクが表示される。

このページは MediaWiki:Noarticletext を編集することでカスタマイズできる。

編集権限がないときは MediaWiki:Noarticletext-nopermission の方が表示される。


### 通常のページの場合

MediaWiki で存在しないページを指定したときに
表示されるページは MediaWiki:Noarticletext がテンプレートとなっている。
このテンプレートは MediaWiki の通常のページと同様に編集可能なので、
メッセージを変更したり、画像を追加したりできる。

編集権限を持たない人がアクセスした場合
(例えばゲストの編集を禁止しているサイトでのゲストアクセスなど)、
MediaWiki:Noarticletext-nopermission が代わりに使用される。
これは Noarticletext とほとんど同じだが、
「このページを編集できます」の部分が
「あなたにはこのページを作成する権限はありません」
となっているなどの違いがある。

それぞれ別個のテンプレートになっているので、
常に同じ画像を埋め込みたい場合は両方に埋め込む必要がある。


### 名前空間が「特別」の場合

名前空間が「特別」で存在しないページを表示しようとした場合、
「そのような特別ページはありません」
(英語表示では "No such special page")
という表示になる。

このメッセージは MediaWiki:Nosuchspecialpage で変更できるが、
これは単体のメッセージのみでページ全体ではない。

MediaWiki:Nosuchspecialpage に設定した内容はすべて
`<h1>`
タグ内に入れられてしまう。

Noarticletext のような、
特別ページ向けのテンプレートがあるかもしれないが
見つけることはできなかった。


## 参考
+ [MediaWiki - Manual:Interface/Noarticletext](https://www.mediawiki.org/wiki/Manual:Interface/Noarticletext)
