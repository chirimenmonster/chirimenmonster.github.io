---
layout: post
title: Django Rest Framework のテストでハマったこと (3)
date: 2021-10-01 22:00 +0900
---

factory_boy の Faker() で、
取得した値を加工してから使用する話。


## Faker() で取得した値の加工

### Faker('address') で住所と建物名を取得する

`Faker('address')` で住所を取得すると、
`'大阪府富津市細野33丁目16番13号 皇居外苑シャルム938'`
のように、
住所と建物名が空白で連結された文字列を取得できる
(建物名が定義されない場合もある)。

```python
class AddressFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = models.Address
    address = factory.Faker('address', locale='ja_JP')
    building = ''   # ← ここに建物名を設定したい
```

```python
# address のインスタンスを取得
address = AddressFactory()
address.address     # '大阪府富津市細野33丁目16番13号 皇居外苑シャルム938' ← 建物名まで含まれる
address.building    # '' ← 設定していないので空文字列のまま
```

これを、
'大阪府富津市細野33丁目16番13号`
と
'皇居外苑シャルム938'
のように、
住所と建物名を分離したレコードとして設定したい。

### Facker() の返す値は直接加工できない

気分としては
`address, building = factory.Faker('address').split()`
というような感じで設定したいのだけれども、
`factory.Facker()` が返すのは文字列ではないのでエラーになる。

```python
    # factory.Facker() が返すのは文字列でないのでエラーになる
    address, building = factory.Faker('address', locale='ja_JP').split()
```

### LazyAttribute() の利用

こういうときは
`LazyAttribute()`
を使用する。
引数に指定した lambda 関数に
インスタンス化されたオブジェクトが渡されるので、
別の変数、例えば `fulladdress` に
`Faker('address')` を設定するようにしておいて、
`LazyAttribute()` で値を取り出して加工した結果を目的の変数に格納する。

```python
    # 一旦別の変数で受けてから LazyAttr で加工する
    fulladdress = factory.Faker('address', locale='ja_JP')
    address = factory.LazyAttribute(lambda o: o.fulladdress.split()[0])
    building = factory.LazyAttribute(lambda o: (o.fulladdress.split() + [''])[1])
```

### exclude で仮の変数を除外する

このままだと、
追加した `fullladderss` は
`models.Address` で定義されていない変数なのでエラーになってしまう。
除外するには、
クラス `Meta` の `exclude` に `fulladdres` を指定すればよい。

```python
class AddressFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = models.Address
        exclude = ('fulladdress',)      # exclude で models.Address に含まれない変数を指定
    fulladdress = factory.Faker('address', locale='ja_JP')
    address = factory.LazyAttribute(lambda o: o.fulladdress.split()[0])
    building = factory.LazyAttribute(lambda o: (o.fulladdress.split() + [''])[1])
```

## 名前の取得例

同様にして、
名前とフリガナのペアを取得し、
それぞれを別の変数に格納できる。
そのようにすると、漢字と読みが対応したレコードを作成できる
(個別に `factory.Faker()` で取得すると、漢字と読みの対応関係が崩れてしまう)。

```python
class PersonFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = models.Person
        exclude = ('firstname_pair', 'lastname_pair')
    firstname_pair = factory.Faker('first_name_pair', locale='ja_JP')
    lastname_pair = factory.Faker('last_name_pair', locale='ja_JP')
    lastname = factory.LazyAttribute(lambda o: o:lastname_pair[0])
    lastname_kana = factory.LazyAttribute(lambda o: o:lastname_pair[1])
    firstname = factory.LazyAttribute(lambda o: o:firstname_pair[0])
    firstname_kana = factory.LazyAttribute(lambda o: o:firstname_pair[1])
```

```python
person = PersonFactory()
person.firstname        # '康弘'
person.firstname_kana   # 'ヤスヒロ'
person.lastname         # '近藤'
person.lastname_kana    # 'コンドウ'
```
