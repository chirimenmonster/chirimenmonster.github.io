---
layout: post
title: Vue CLI のインストール
---

[Vue CLI: Instant Prototyping](https://cli.vuejs.org/guide/prototyping.html)
[Vue CLI: Creating a Project](https://cli.vuejs.org/guide/creating-a-project.html)
に従って、
Vue CLI のインストールから
プロジェクトの作成、ビルドまでの一連の手順をやってみる。

## インストール

[Vue CLI: Instant Prototyping](https://cli.vuejs.org/guide/prototyping.html)
に従って
@vue/cli と @vue/cli-service-global
の2つのパッケージをインストールする。

```
npm install -g @vue/cli @vue/cli-service-global
```

インストールを実行すると、
依存関係でインストールされてるらしいパッケージのいくつかに
deprected だっていう Warning が出てきた。
問題ないのかな。

```
PS D:\test> npm install -g @vue/cli @vue/cli-service-global
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated @hapi/joi@15.1.1: Switch to 'npm install joi'
npm WARN deprecated @hapi/hoek@8.5.1: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/bourne@1.3.2: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/address@2.1.4: Moved to 'npm install @sideway/address'
npm WARN deprecated @hapi/topo@3.1.6: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated uuid@3.4.0: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated babel-eslint@10.1.0: babel-eslint is now @babel/eslint-parser. This package will no longer receive updates.
npm WARN deprecated querystring@0.2.0: The
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated eslint-loader@2.2.1: This loader has been deprecated. Please use eslint-webpack-plugin
npm WARN deprecated html-webpack-plugin@3.2.0: 3.x is no longer supported
C:\Users\myname\AppData\Roaming\npm\vue -> C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\bin\vue.js

> yorkie@2.0.0 install C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli-service-global\node_modules\yorkie
> node bin/install.js

setting up Git hooks
trying to install from sub 'node_module' directory, skipping Git hooks installation

> core-js@3.15.2 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli-service-global\node_modules\core-js
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> ejs@2.7.4 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli-service-global\node_modules\ejs
> node ./postinstall.js

Thank you for installing EJS: built with the Jake JavaScript build tool (https://jakejs.com/)


> core-js-pure@3.15.2 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\node_modules\core-js-pure
> node -e "try{require('./postinstall')}catch(e){}"


> @apollo/protobufjs@1.2.2 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\node_modules\@apollo\protobufjs
> node scripts/postinstall


> ejs@2.7.4 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\node_modules\ejs
> node ./postinstall.js

Thank you for installing EJS: built with the Jake JavaScript build tool (https://jakejs.com/)

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@~2.3.2 (node_modules\@vue\cli-service-global\node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules\@vue\cli-service-global\node_modules\watchpack-chokidar2\node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules\@vue\cli-service-global\node_modules\webpack-dev-server\node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ @vue/cli@4.5.13
+ @vue/cli-service-global@4.5.13
added 2259 packages from 852 contributors in 174.198s
```

インストールが完了したのでバージョンを確認。

```
PS D:\test> vue --version
@vue/cli 4.5.13
```

## プロジェクトの作成

[Vue CLI: Creating a Project](https://cli.vuejs.org/guide/creating-a-project.html)
に従って、
hello-world という名前の新規プロジェクトを作成する。

```
PS D:\test> vue create hello-world
```

`vue create` を実行すると、
次のようにプロジェクトを Vue 2 用として作成するか、
Vue 3 用として作成するかを選択するメニューが表示される。

```
Vue CLI v4.5.13
? Please pick a preset: (Use arrow keys)
  Default ([Vue 2] babel, eslint)
> Default (Vue 3) ([Vue 3] babel, eslint)
  Manually select features
```

ここでは、Vue 3 用を選択する。

```
Vue CLI v4.5.13
? Please pick a preset: Default (Vue 3) ([Vue 3] babel, eslint)


Vue CLI v4.5.13
✨  Creating project in D:\test\hello-world.
🗃  Initializing git repository...
⚙️  Installing CLI plugins. This might take a while...


> yorkie@2.0.0 install D:\test\hello-world\node_modules\yorkie
> node bin/install.js

setting up Git hooks
done


> core-js@3.15.2 postinstall D:\test\hello-world\node_modules\core-js
> node -e "try{require('./postinstall')}catch(e){}"


> ejs@2.7.4 postinstall D:\test\hello-world\node_modules\ejs
> node ./postinstall.js

added 1255 packages from 655 contributors and audited 1258 packages in 60.256s

78 packages are looking for funding
  run `npm fund` for details

found 10 vulnerabilities (7 moderate, 3 high)
  run `npm audit fix` to fix them, or `npm audit` for details
🚀  Invoking generators...
📦  Installing additional dependencies...

added 83 packages from 87 contributors and audited 1341 packages in 11.303s

85 packages are looking for funding
  run `npm fund` for details

found 10 vulnerabilities (7 moderate, 3 high)
  run `npm audit fix` to fix them, or `npm audit` for details
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project hello-world.
👉  Get started with the following commands:

 $ cd hello-world
 $ npm run serve

```


## 動作確認

ローカルサーバの実行はプロジェクトのフォルダに移動して
`npm run serve` を実行する。
`App.vue` を置いているフォルダで `vue serve` を実行するのではない。

デフォルトでは localhost:8080 で接続できる。

```
PS D:\test> cd hello-world
PS D:\test\hello-world> npm run serve

> hello-world@0.1.0 serve D:\test\hello-world
> vue-cli-service serve

 INFO  Starting development server...
98% after emitting CopyPlugin

 DONE  Compiled successfully in 2376ms                                                                        20:47:13


  App running at:
  - Local:   http://localhost:8080/
  - Network: http://192.168.0.142:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```


## コンパイル

ウェブサーバに設置するファイルためのを作成するには、
プロジェクトのフォルダで
`npm run build` を実行する。
ファイルは `dist` フォルダに作成される。

```
PS D:\test\hello-world> npm run build

> hello-world@0.1.0 build D:\test\hello-world
> vue-cli-service build


/  Building for production...

 DONE  Compiled successfully in 37307ms                                                                       21:43:04
  File                                 Size                                                  Gzipped

  dist\js\chunk-vendors.686215c5.js    82.11 KiB                                             30.81 KiB
  dist\js\app.de390782.js              4.52 KiB                                              1.61 KiB
  dist\css\app.fb0c6e1c.css            0.33 KiB                                              0.23 KiB

  Images and other types of assets omitted.

 DONE  Build complete. The dist directory is ready to be deployed.
 INFO  Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html
```


dist に作成されたファイルはウェブサーバに設置する前提で作成されているので、
サイト内のファイル参照は '/' で開始する絶対パスになっている。
そのため、ローカルのブラウザでファイルを直接開いた場合にはリンクが正しく機能しない。
ローカルのブラウザで動作確認できるようにするには、
'./' で開始する相対パスになるように設定を変更する必要がある。

vue.config.js を作成し、以下のように `publicPath` の設定を行う。

```
module.exports = {
    publicPath: './'
}
```
