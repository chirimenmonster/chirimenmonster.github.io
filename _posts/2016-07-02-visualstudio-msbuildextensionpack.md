---
layout: post
title: MSBuild Extension Pack を導入
date: 2016-07-02 11:00 +0900
---

## 概要

MSBuild で手軽に zip 圧縮するには MSBuild Extension Pack を入れればよいらしい。


## 手順

+ [MSBuild Extension Pack](http://www.msbuildextensionpack.com/) のページから MSBuild.Extension.Pack.4.0.13.0.zip をダウンロード
+ 展開して 4.0.13.0\x64 にあるインストーラ MSBuild Extension Pack 4 (x64).msi を実行する (デフォルトで C:\Program Files\MSBuild\ExtensionPack\4.0\ にインストールされる)

## 使い方

MSBuild.ExtensionPack.tasks をインポートする。
ExtentionTasksPath には MSBuild.ExtensionPack.tasks へのパスを設定しておく。
パスの末尾は "\\" で終了するようにする。

{% highlight xml %}
<PropertyGroup>
    <ExtensionTasksPath>c:\Program Files\MSBuild\ExtensionPack\4.0\</ExtensionTasksPath>
</PropertyGroup>
<Import Project="$(ExtensionTasksPath)MSBuild.ExtensionPack.tasks" />

<MSBuild.ExtensionPack.Compression.Zip TaskAction="Create"
    CompressFiles="@(TS3Files)" RemoveRoot="$(OutputDir)"
    ZipFileName="$(OutputDir)\$(AppName).ts3_plugin" />
{% endhighlight %}
        