---
layout: post
title: MediaWiki のスパム対策
date: 2016-02-04 09:03:00 +0900
---
MediaWiki にスパムがきたので対策メモ

# 概要

管理してる MediaWiki のページをちょっと目を離していたら
スパムがいっぱいきてた。
大量にアカウント登録して新規ページをいっぱい作成している。

とりあえずすぐできそうな対策から実行してみる。


## Captcha の導入

いくつかの Captcha
が[標準で使用でき](https://www.mediawiki.org/wiki/Extension:ConfirmEdit)て、
中でも [QuestyCaptcha](https://www.mediawiki.org/wiki/Special:MyLanguage/Extension:QuestyCaptcha)が[有効](http://azeha.lv9.org/w/?title=MediaWiki%E3%82%B9%E3%83%91%E3%83%A0%E5%AF%BE%E7%AD%96)らしい。

{% highlight php startinline %}
require_once "$IP/extensions/ConfirmEdit/ConfirmEdit.php";
wfLoadExtension('ConfirmEdit/QuestyCaptcha');
$wgCaptchaClass = 'QuestyCaptcha';
$wgCaptchaQuestions[] = array( 'question' => "指定の問い", 'answer' => "対応する答え");
$wgCaptchaTriggers['edit'] = true;
$wgCaptchaTriggers['create'] = true;
$wgGroupPermissions['sysop']['skipcaptcha'] = false;
{% endhighlight %}

配列 `$wgCapthaQuestion` で問いと答えのペアを登録する。
複数の指定が可能。日本語もOK。

`$wgCaptchaTriggers['edit'] = true` はページ編集時に Captcha を有効にする設定。
同じく `$wgCaptchaTriggers['create'] = true` はページ作成時。

`$wgGroupPermissions['sysop']['skipcaptcha'] = false;`
は sysop グループに対しても Captcha を要求する設定。


## UserMerge の導入

大量に登録されてしまった Spammer だけど、
MediaWiki ではユーザの削除は基本的にできないらしい。

代わりにユーザーを統合する拡張があるので Spammer は
全部 Spammer というユーザに統合しておこう。

{% highlight php startinline %}
require_once "$IP/extensions/UserMerge/UserMerge.php";
$wgGroupPermissions['bureaucrat']['usermerge'] = true;
{% endhighlight %}

作業は一人ずつ手作業で統合していくので結構めんどうくさい。



# 参考
+ [MediaWikiスパム対策](http://azeha.lv9.org/w/?title=MediaWiki%E3%82%B9%E3%83%91%E3%83%A0%E5%AF%BE%E7%AD%96)
+ [MediaWiki:Extension:ConfirmEdit](https://www.mediawiki.org/wiki/Extension:ConfirmEdit)
+ [MediaWiki:Extension:UserMerge](https://www.mediawiki.org/wiki/Extension:UserMerge)
