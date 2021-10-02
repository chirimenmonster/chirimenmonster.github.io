---
layout: post
title: Django Rest Framework のテストでハマったこと (4)
---

Django で既存データベースから inspectdb で作成した
models.my は `managed = False` となっている。
そのままだと test を実行したときに、
テスト用データベースにモデルに対応したテーブルが作成されない。

## inspectdb による既存データベースの利用

既存のデータベースを Django で使用するとき、
データベースに対応したモデル定義を
`python managed.py inspectdb`
で取得することができる
([Integrating Django with a legacy database](https://docs.djangoproject.com/en/3.2/howto/legacy-databases/),
[[Django]既存のデータベースを利用する方法](https://qiita.com/okoppe8/items/82a9cc2bb99de05f8dbb))。

```
python managed inspectdb > models.py
```

このとき取得したモデル定義は、
`managed = False`
となっている。
既存データベースだから
Django
側からテーブル定義を変更しない、
ということでこういう設定なのだろう。

```python
    class Meta:
        managed = False
```

## テスト実行時のエラー (Table doesn't exist)

サーバは特に支障なく動いていたのだが、
テストを実行する習慣をつけようとテストコードを書いて
`python manage.py test`
を実行すると、
"Table 'test_XXX.XXX' doesn't exitst"
のエラーが。

```
django.db.utils.ProgrammingError: (1146, "Table 'test_XXX.XXX' doesn't exist")
```

`--keepdb` でデータベースの内容を確認すると、
モデル定義に対応したテーブルが作成されていない。

そもそも `migrations` が空なので、作成されていないのだ。

## makemigration (本番環境では不要) を実行

`python manage.py makemigrations` を実行して、
`migrations` にファイル `0001_initial.py` ができていることを確認してから、
再度 `python manage.py test`。

"Table 'test_XXX.XXX' doesn't exitst"

## モデル定義を managed = True にしてから makemigration

これは
models.py
で
`managed = False`
のままになっていたことを忘れていたせい。

`managed = True` に書き換えて `python manage.py makemigrations` 実行。
今度は `migrations` にファイル `0002_auto_XXXXXXXX_XXXX.py` が作成された。

再々度 `python manage.py test`。

"Table 'test_XXX.XXX' doesn't exitst"。

やはりテーブルは作成されていない。

## [解決] migrations のファイルをクリアしてから makemigration をやり直し

よくわからないが初回のマイグレーションファイル
`0001_initial.py`
で
`managed: False`
に設定されたモデルは、
後続のマイグレーションファイルで
`managed: True`
となっても無視されてしまうようだ。

マイグレーションファイルをすべて消去して、
`makemgigrations`
をやり直すとテスト実行時にテーブルが作成されるようになった。

## その他

かつて
[DjangoTestSuiteRunner のサブクラスでテスト時に managed の設定を書き換える方法](https://stackoverflow.com/questions/7020966/how-to-create-table-during-django-tests-with-managed-false/7035002#7035002)
が提案されていたようだが、
[後継の DiscoverRunner でやってみたけど機能しない](https://stackoverflow.com/questions/36986369/testing-django-application-with-several-legacy-databases)
ようである。

[モデルの _meta.managed を設定する方法](https://www.ytyng.com/blog/django-unmanaged-model-unit-test/)
も試してみたけど機能しなかった。
