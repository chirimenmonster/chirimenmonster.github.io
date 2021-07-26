---
layout: post
title: 既存データベースを Django REST framework で使用する
date: 2021-07-26 15:00 +0900
---
既存のデータベースを Django REST framework で使用できるように設定してみる。
データベースは NAS 上の MariaDB で、住所録が格納されている。
Django, Django REST framework は pip でインストール済み。


## プロジェクトフォルダの作成

[Django のチュートリアル](https://docs.djangoproject.com/ja/3.2/intro/tutorial01/#creating-a-project)の手順と同様に
`django-admin startproject` で Django のプロジェクトを作成する。
指定したプロジェクト名のフォルダが作成され、Django のプロジェクトに必要なファイル群が生成される。

プロジェクト名はなんでもよいのだけれど、ここではチュートリアルと同じ `mysite` とした。

新規作成されたプロジェクト名のフォルダの中には、
管理ツール `manager.py` とプロジェクト名と同名のフォルダが生成されている。
親子で同名のフォルダなのでややこしい。

```
PS D:\test> django-admin startproject mysite
PS D:\test> dir mysite


    ディレクトリ: D:\test\mysite


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2021/07/25     14:43                mysite
-a----        2021/07/25     14:43            684 manage.py


```

[Django REST framework のチュートリアル](https://www.django-rest-framework.org/tutorial/quickstart/#project-setup)だと、
新規フォルダを作成してからフォルダを指定して `startproject` するようになってるけど、
どちらでも同じことなのだろう。

```
PS D:\test> mkdir mysite2
PS D:\test> cd mysite2
PS D:\test\mysite2> django-admin startproject mysite2 .
```

## アプリケーションの作成

`django-admin startapp` でアプリケーションを作成するのだけれど、
Django のチュートリアルでは `manage.py` のあるフォルダ、
Django REST framework のチュートリアルでは子のプロジェクト名のフォルダで実行している。

どちらがよいのかよくわからないので、REST framework の方の構成で実行する。
住所録データベースを操作するアプリケーションを作成するのでアプリケーション名は addressbook とした。

```
PS D:\test> cd mysite/mysite
PS D:\test\mysite\mysite> django-admin startapp addressbook
PS D:\test\mysite\mysite> dir


    ディレクトリ: D:\test\mysite\mysite


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2021/07/25     14:46                addressbook
-a----        2021/07/25     14:43            405 asgi.py
-a----        2021/07/25     14:43           3364 settings.py
-a----        2021/07/25     14:43            769 urls.py
-a----        2021/07/25     14:43            405 wsgi.py
-a----        2021/07/25     14:43              0 __init__.py

```

```
PS D:\test\mysite\mysite> dir addressbook


    ディレクトリ: D:\test\mysite\mysite\addressbook


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2021/07/25     14:46                migrations
-a----        2021/07/25     14:46             66 admin.py
-a----        2021/07/25     14:46            160 apps.py
-a----        2021/07/25     14:46             60 models.py
-a----        2021/07/25     14:46             63 tests.py
-a----        2021/07/25     14:46             66 views.py
-a----        2021/07/25     14:46              0 __init__.py

```

## データベース接続先の設定

データベースの接続先は `mysite/settings.py` で指定する。
[公式ドキュメントの記述方法](https://docs.djangoproject.com/ja/3.2/ref/settings/#databases)を見ながら設定。

住所録のデータベースは、データベース名、接続ユーザー名ともに `addressbook` として設定されている。
ホスト名は `NAS` でポート番号はデフォルトの `3306` である。

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'addressbook',
        'USER': 'addressbook',
        'PASSWORD': '(パスワード文字列)',
        'HOST': 'NAS',
        'PORT': '3306',
    }
}
```

`manage.py inspectdb` を使って既存データベースから Django のモデル定義を生成することができる。
結果は標準出力に送られるので、リダイレクトでファイルに保存すればよい、
が少し注意する点がある。

```
PS D:\test\mysite> py -3 manage.py inspectdb > mysite\addressbook\models.py
```

実は上記のようにすると、
`models.py` に null が含まれているとして、
Django を実行したときにエラーが発生してしまう。

```
ValueError: source code string cannot contain null bytes
```

これはファイルのエンコーディングが UTF-16 LE になっていて、
UTF-8 として読み込むと余分な null 文字が含まれているように解釈されるためだ。
おそらく PowerShell でリダイレクトしたことが原因だと思われる。

何度も実行するような作業ではないので、
適当なエディタで開いた後、UTF-8 で保存し直すのが手っ取り早いだろう。

## Model

`inspectdb` で生成されたモデル定義の一部。
実際は複数のテーブルが定義されているけれど省略した。


```python
from django.db import models

class Family(models.Model):
    zipcode = models.TextField(blank=True, null=True)
    address = models.TextField(blank=True, null=True)
    building = models.TextField(blank=True, null=True)
    tel = models.TextField(blank=True, null=True)
    fax = models.TextField(blank=True, null=True)
    comment = models.TextField(blank=True, null=True)

    class Meta:
        managed = False
        db_table = 'family'
```

データベースの方のテーブル定義は次のようになっていて、
ここから自動生成されている。

```sql
CREATE TABLE `family` (
  `id` int(11) NOT NULL,
  `zipcode` text,
  `address` text,
  `building` text,
  `tel` text,
  `fax` text,
  `comment` text
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## Serializer の作成

Serializer が何かはあまりよくわかっていないのだけれども、
チュートリアルをなぞるような感じで作成していけばそのうちにイメージが掴めてくるような気がする。

アプリケーションフィルダに
`serializers.py`
という名称でファイルを作成。
`models.py` で定義した `Family` のシリアライザとして
`serializers.HyperlinkedModelSerializer`
を継承したクラス `FamilySetrializer` を定義する。 

`fields` にはシリアライズ対象の項目として、
モデル `Family` のプロパティを列挙するが、
`url` は `HyperlinkedModelSerializer` で用意されるプロパティである。

```python
from rest_framework import serializers
from .models import Family

class FamilySerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Family
        fields = ['url', 'zipcode', 'address', 'building', 'tel', 'fax', 'comment']
```

## View の作成

アプリケーションフォルダの `views.py` に記述する。

```python
from django.shortcuts import render
from rest_framework import viewsets
from .models import Family
from .serializers import FamilySerializer

class FamilyViewSet(viewsets.ModelViewSet):
    queryset = Family.objects.all().order_by('id')
    serializer_class = FamilySerializer
```

## Urls の設定

`urls.py` への記述。
先程作成した FamilyViewSet は `router.register` で登録する。
ここで `register` の第1引数に指定した `families` が url の一部になるので
`/families` でアクセスすると `views.FamilyViewSet` が呼び出されることになる。

```python
from django import urls
from django.contrib import admin
from django.urls import path, include
from rest_framework import routers

from mysite.addressbook import views

router = routers.DefaultRouter()
router.register(r'families', views.FamilyViewSet)

urlpatterns = [
    path('', include(router.urls)),
    path('admin/', admin.site.urls),
]
```

## Settings に設定を追加する

Django REST framework を使用するため
`settings.py` の `INSTALLED_APPS` に `rest_framework` を追加する。
プロジェクトのフォルダ名 `mysite` も指定する。

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'mysite',
]
```

## 確認

`manage.py runserver` でサーバーを実行して確認できる。

```
PS D:\test\mysite> py -3 manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
July 26, 2021 - 13:44:21
Django version 3.2.5, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.

```

ブラウザで `http://localhost:8000/families/` にアクセスした例。


![確認](/images/2021-07-26-django-rest-apitest.png)
