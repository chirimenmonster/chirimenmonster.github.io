---
layout: post
title: Vue CLI のインストール
---
Vue CLI をインストールした。
依存関係でインストールされてるらしいパッケージのいくつかに
deprected だっていう Warning が出てるけど問題ないのかな。

```
PS D:\test\myapp> npm install -g @vue/cli
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated @hapi/joi@15.1.1: Switch to 'npm install joi'
npm WARN deprecated @hapi/bourne@1.3.2: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/topo@3.1.6: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/address@2.1.4: Moved to 'npm install @sideway/address'
npm WARN deprecated @hapi/hoek@8.5.1: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated uuid@3.4.0: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
C:\Users\myname\AppData\Roaming\npm\vue -> C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\bin\vue.js

> core-js-pure@3.15.2 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\node_modules\core-js-pure
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> @apollo/protobufjs@1.2.2 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\node_modules\@apollo\protobufjs
> node scripts/postinstall


> ejs@2.7.4 postinstall C:\Users\myname\AppData\Roaming\npm\node_modules\@vue\cli\node_modules\ejs
> node ./postinstall.js

Thank you for installing EJS: built with the Jake JavaScript build tool (https://jakejs.com/)

+ @vue/cli@4.5.13
added 928 packages from 583 contributors in 292.951s
```


```
PS D:\test\myapp> vue --version
@vue/cli 4.5.13
```

[Vue CLI: Instant Prototyping](https://cli.vuejs.org/guide/prototyping.html)
に従って、
下記内容の `App.vue` ファイルを作成。

```html
<template>
  <h1>Hello!</h1>
</template>
```

```
PS E:\test\myapp> vue create test
?  Your connection to the default npm registry seems to be slow.
   Use https://registry.npm.taobao.org for faster installation? (Y/n)
```


```
PS E:\test\myapp\test> vue serve

  Command vue serve requires a global addon to be installed.
  Please run npm i -g @vue/cli-service-global and try again.
```

@vue/cli と @vue/cli-service-global をインストールする。

```
PS E:\test\webapp> npm i -g @vue/cli @vue/cli-service-global
```

```
PS E:\test\webapp> vue create hello-world
 ERROR  Failed to get response from https://registry.npmjs.org/vue-cli-version-marker


Vue CLI v4.5.13
Failed to check for updates
? Please pick a preset: Default (Vue 3) ([Vue 3] babel, eslint)


Vue CLI v4.5.13
Failed to check for updates
✨  Creating project in E:\test\webapp\hello-world.
🗃  Initializing git repository...
⚙️  Installing CLI plugins. This might take a while...


> yorkie@2.0.0 install E:\test\webapp\hello-world\node_modules\yorkie
> node bin/install.js

setting up Git hooks
done


> core-js@3.15.2 postinstall E:\test\webapp\hello-world\node_modules\core-js
> node -e "try{require('./postinstall')}catch(e){}"


> ejs@2.7.4 postinstall E:\test\webapp\hello-world\node_modules\ejs
> node ./postinstall.js

added 1255 packages from 655 contributors and audited 1258 packages in 65.163s

78 packages are looking for funding
  run `npm fund` for details

found 10 vulnerabilities (7 moderate, 3 high)
  run `npm audit fix` to fix them, or `npm audit` for details
🚀  Invoking generators...
📦  Installing additional dependencies...

added 83 packages from 87 contributors and audited 1341 packages in 12.242s

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

`npm run serve` で動作確認を行う。
デフォルトでは localhost:8080 で接続できる。

```
npm run serve
```

エラーが出た。
template は1つの要素しか持てないらしい。

```
D:\test\myapp\test\src\App.vue
  3:3  error  The template root requires exactly one element  vue/no-multiple-template-root

✖ 1 problem (1 error, 0 warnings)
```

App.vue を見ると要素が2つあったので

```html
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>
```

`<div>`, `</div>` で囲ってやるとエラーが消えた。


```html
<template>
  <div>
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>
```



## コンパイル

`npm run build` でサーバに設置するファイルを作成する。

```
npm run build
```


dist に作成されたファイルはサーバに設置する前提で作成されているので、
サイト内のファイル参照が '/' で開始する絶対パスになっており、
ローカルのブラウザで開こうとしてもリンクが正しく機能しない。
ローカルのブラウザで動作確認できるようにするには、
'./' で開始する相対パスになるように設定を変更する必要がある。

vue.config.js を作成し、以下のように `publicPath` の設定を行う。

```
module.exports = {
    publicPath: './'
}
```
