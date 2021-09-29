---
layout: post
title: Django Rest Framework のテストでハマったこと (1)
date: 2021-09-29 21:20 +0900
---

Django Rest Framework のテストコードの例でよく出てくる
`reverse('user-list')`
というような記述。
ここに出てくる `user-list` の部分って、
何を書いたらいいの? って話。

## はじめに

Django Rest Framework でのテストの書き方を調べていたところ、
URL を取得するのに
`reverse('user-list')`
というような記述例をよく見かけた。

`user` はモデルの名前だとして、その後の `-list` ってなんだろう？

手元で試すと `user` では動かなくて `user-list` だと動くのだけれども、
そもそも
`reverse()`
に何を指定すべきかよくわからないのだ。

## django.urls.reverse のドキュメント

公式ドキュメント django.urls の
[reverse()](https://docs.djangoproject.com/en/3.2/ref/urlresolvers/#reverse)
を見ると、

```
If you need to use something similar to the url template tag in your code, Django provides the following function:

reverse(viewname, urlconf=None, args=None, kwargs=None, current_app=None)
```

とあって、引数には
**viewname**
を指定するらしい。
**viewname**
は `path()` で指定した名前、
`urls.py` の `urlpatterns` で指定したやつのようだけれども、
自分のコードだと次のようになっていて、
**viewname** を明示的に指定していない。

```python
router = routers.DefaultRouter()
router.register(r'person', views.PersonViewSet)
(略)

urlpatterns = [
    path('addressbook/', include(router.urls)),
    path('admin/', admin.site.urls),
]
```

上のコードだと `router.urls` をインクルードしているので、
routers (rest_framework.routers) について調べる必要がありそうだ。

## rest_framework.routers のドキュメント

Django REST framework の公式ドキュメント
[Routers](https://www.django-rest-framework.org/api-guide/routers/)
を参照すると


> The example above would generate the following URL patterns:
> 
> URL pattern: `^users/$` Name: `'user-list'`<br>
> URL pattern: `^users/{pk}/$` Name: `'user-detail'`<br>
> URL pattern: `^accounts/$` Name: `'account-list'`<br>
> URL pattern: `^accounts/{pk}/$` Name: `'account-detail'`

とあって、
サフィックス
`-list`, `-detail`
が router によって登録されていたことがわかる。

[DefaultRouter](https://www.django-rest-framework.org/api-guide/routers/#defaultrouter)
の場合の完全な表は次のようになっていて、
これで `reverse()` の引数に何を指定すればいいかがわかるようになった。

![DefaultRouter](/images/2021-09-29-django-rest-defaultrouter.png)

## おわりに

上記のことが、
当初調べていてもさっぱり検索に引っかかってこなかった。
探し方が悪いのかもしれないし、
慣れたら当たり前のようになって気にならなくなるのかもしれないけれど、
割と行き詰まった感があったので、
初心を忘れないうちに書き留めておく。
