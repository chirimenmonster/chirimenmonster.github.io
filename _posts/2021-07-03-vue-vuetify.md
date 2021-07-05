---
layout: post
title: Vuetify
---

Vuetify の公式ドキュメント
[Get started with Vuetify Alpha](https://next.vuetifyjs.com/ja/getting-started/installation/)
。
まだ Alpha 版だけど、
他の主要なフレームワークはまだ Vue 3 に対応していない。


vue-cli 4.0 は導入済みで、
Vue 3 のプロジェクトも作成済みなので、
そのまま vuetify のインストールへ。

```
cd my-app
vue add vuetify
```

```
PS D:\test\hello-world> vue add vuetify
 WARN  There are uncommitted changes in the current repository, it's recommended to commit or stash them first.
? Still proceed? (y/N)
```

プロジェクトにコミットしていない変更があると確認を求められる。
git を使用していなくても同じ。

```
? Still proceed? Yes

📦  Installing vue-cli-plugin-vuetify...

+ vue-cli-plugin-vuetify@2.4.1
added 9 packages from 11 contributors and audited 1352 packages in 7.863s

85 packages are looking for funding
  run `npm fund` for details

found 10 vulnerabilities (7 moderate, 3 high)
  run `npm audit fix` to fix them, or `npm audit` for details
✔  Successfully installed plugin: vue-cli-plugin-vuetify

? Choose a preset: (Use arrow keys)
> Default (recommended)
  Preview (Vuetify 3 + Vite)
  Prototype (rapid development)
  V3 (alpha)
  Configure (advanced)
```

インストールするパッケージの種類を聞かれるので
｀V3 (alpha)` を選択する。


```
? Choose a preset: V3 (alpha)

🚀  Invoking generator for vue-cli-plugin-vuetify...
📦  Installing additional dependencies...

added 11 packages from 10 contributors and audited 1363 packages in 9.542s

88 packages are looking for funding
  run `npm fund` for details

found 10 vulnerabilities (7 moderate, 3 high)
  run `npm audit fix` to fix them, or `npm audit` for details
⚓  Running completion hooks...

✔  Successfully invoked generator for plugin: vue-cli-plugin-vuetify
 vuetify  Discord community: https://community.vuetifyjs.com
 vuetify  Github: https://github.com/vuetifyjs/vuetify
 vuetify  Support Vuetify: https://github.com/sponsors/johnleider
```

main.js に次の行が追加されていた。
ドキュメントとちょっと違うなあ。

```
import vuetify from './plugins/vuetify'

const app = createApp(App)
app.use(vuetify)
```

main.js に記述していた axios は消えた。
以前の変更をコミットまたは退避していない場合は注意。


Google の Roboto フォントと Material Design アイコンを使うそうなので
下記の行を index.html に追加。

```
<link href="https://fonts.googleapis.com/css?family=Roboto:100,300,400,500,700,900" rel="stylesheet">
<link href="https://cdn.jsdelivr.net/npm/@mdi/font@4.x/css/materialdesignicons.min.css" rel="stylesheet">
```

App.vue も書き換えられていた。

```
<template>
  <v-app>
    <v-main>
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
}
</script>
```

ブラウザにエラー表示が出た。sass モジュールがないと。

```
Failed to compile.

./node_modules/vuetify/lib/styles/main.sass (./node_modules/css-loader/dist/cjs.js??ref--9-oneOf-3-1!./node_modules/postcss-loader/src??ref--9-oneOf-3-2!./node_modules/sass-loader/dist/cjs.js??ref--9-oneOf-3-3!./node_modules/vuetify/lib/styles/main.sass)
Module Error (from ./node_modules/sass-loader/dist/cjs.js):
Cannot find module 'sass'
Require stack:
- D:\test\hello-world\node_modules\sass-loader\dist\utils.js
(snip)
```

node-sass をインストールする。

```
PS D:\test\hello-world> npm install node-sass

> node-sass@6.0.1 install D:\test\hello-world\node_modules\node-sass
> node scripts/install.js

Downloading binary from https://github.com/sass/node-sass/releases/download/v6.0.1/win32-x64-83_binding.node
Download complete .] - :
Binary saved to D:\test\hello-world\node_modules\node-sass\vendor\win32-x64-83\binding.node
Caching binary to C:\Users\mrkjy\AppData\Roaming\npm-cache\node-sass\6.0.1\win32-x64-83_binding.node

> node-sass@6.0.1 postinstall D:\test\hello-world\node_modules\node-sass
> node scripts/build.js

Binary found at D:\test\hello-world\node_modules\node-sass\vendor\win32-x64-83\binding.node
Testing binary
Binary is fine
npm WARN postcss-modules@4.1.3 requires a peer of postcss@^8.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN icss-utils@5.1.0 requires a peer of postcss@^8.1.0 but none is installed. You must install peer dependencies yourself.
npm WARN postcss-modules-extract-imports@3.0.0 requires a peer of postcss@^8.1.0 but none is installed. You must install peer dependencies yourself.
npm WARN postcss-modules-local-by-default@4.0.0 requires a peer of postcss@^8.1.0 but none is installed. You must install peer dependencies yourself.
npm WARN postcss-modules-scope@3.0.0 requires a peer of postcss@^8.1.0 but none is installed. You must install peer dependencies yourself.
npm WARN postcss-modules-values@4.0.0 requires a peer of postcss@^8.1.0 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.3.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.13 (node_modules\watchpack-chokidar2\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.13 (node_modules\webpack-dev-server\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ node-sass@6.0.1
added 95 packages from 65 contributors and audited 1458 packages in 19.79s

94 packages are looking for funding
  run `npm fund` for details

found 10 vulnerabilities (7 moderate, 3 high)
  run `npm audit fix` to fix them, or `npm audit` for details
```


![Vuetify のインストール]({{ site.baseurl }}/images/2021-07-03-vuetify-install.png)
