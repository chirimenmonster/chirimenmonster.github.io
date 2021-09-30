---
layout: post
title: Django Rest Framework のテストでハマったこと (2)
date: 2021-09-30 22:00 +0900
---
Django のテスト用データを作成するのによく用いられる
factory_boy
で locale を指定して日本語圏用のデータを利用する話。

## はじめに

Django のテストでテスト用のデータを作成するのに
[factory_boy](https://factoryboy.readthedocs.io/en/stable/)
が便利である。

実際のテスト用のダミーデータの作成には
[Faker](https://faker.readthedocs.io/en/master/)
が呼び出されるようになっていて、
locale を指定することで、
[日本語用のデータセット](https://faker.readthedocs.io/en/master/locales/ja_JP.html)を使用することができる。

## 引数での locale 指定

factory_boy での locale 指定は
`Faker` のキーワード引数 `locale` で指定する。
次に示すのは公式ドキュメントでの例であるが、
`ja_JP` を指定すれば日本人名が得られる。

```python
class UserFactory(factory.Factory):
    class Meta:
        model = User

    name = factory.Faker('name', locale='fr_FR')
```

`User` は Django の model で、別の箇所で定義されているものとする。

ここで `UserFactory` をインスタンス化すると、
プロパティ name にフランス locale (fr_FR) の人名がセットされる
(インスタンス化を行うたびに異なる値がセットされる)。

```python
user = UserFactory()
user.name   # 'Jean Valjean'
```

locale が `ja_JP` であれば、
日本語圏の人名がセットされる。

```python
    name = factory.Faker('name', locale='ja_JP')
```

```python
user = UserFactory()
user.name   # '前田 舞'
```

## クラスメソッド override_default_locale

locale を頻繁に切り替えるようなシステムでなければ、
毎回キーワード引数を指定するのではなく、
デフォルトを `ja_JP` に変更したい。

そう思って調べてみると、
`override_default_locale`
という、いかにもそれっぽいクラスメソッドが
`factory.Faker`
に用意されていた。

が、Factory を定義するユーザーコードで

```python
faker.override_default_locale('ja_JP')
```

を実行してもデフォルトロケールの変更は有効にならない。
model がインスタンス化されるところで
`override_default_locale`
が有効になっていないと駄目なようだ。

なので、さっきの例だと
`UserFactory()`
を実行するモジュールのところで実行してやる必要がある
(公式ドキュメントに記載されている)。

```python
with factory.Faker.override_default_locale('ja_JP'):
    UserFactory()
```

これだと `UserFactroy` の呼び出し側で
factory_boy
をインポートする (`import factory`) ことになる、
(locale の設定のためだけにわざわざインポートすることになる)
ので、
コードの構成によってはあまりうれしくない。


## クラス変数 _DEFAULT_LOCALE

どうしても Factory を定義するモジュールでデフォルト設定をしたい場合は、
非公式ではあるが `Faker._DEFAULT_LOCALE` を直接設定する方法がある。

```python
factory.Faker._DEFAULT_LOCALE = 'ja_JP'

class UserFactory(factory.Factory):
    class Meta:
        model = User

    name = factory.Faker('name')
```

このようにすれば、
手軽にデフォルトの locale を変更できる。
