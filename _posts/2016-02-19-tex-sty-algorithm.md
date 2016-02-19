---
layout: post
title: LaTeX でアルゴリズムを記述する
date: 2016-02-19 16:24:00 +0900
---
LaTeX でアルゴリズムを記述するときにはどのスタイルを使用したらいいのか。

## はじめに

LaTeX (TeX Live 2015) でアルゴリズムを記述しようとしたとき、
指定するパッケージ名や環境、そもそも使い方を覚えてなかったので
Google で検索することにした。

以前記述したときのファイルを探して確認すればいいんだけど、
検索したほうが早いかと思って。

そうしたら
[LaTeXのよくある悪習とその正しい対策](http://prml.main.ist.hokudai.ac.jp/~ryo/contents/suuri2014/suuri.pdf)
という PDF 文書に

> 実際に擬似コードを書く部分で用いるパッケージ algorithmic は古いパッケージ．
> algpseudocode等のパッケージが最新．

などと書いてある。

algpseudocode で検索すると
[algorithmicx](https://www.ctan.org/pkg/algorithmicx)
というのがでてきて、
使用例でも algpseudocode 環境を使ってたので、
ははーん、これのことかと思ってそのときはそれを使った。

だけど、以前自分が使ったのは algorithmic でなくて
[algorithm2e](https://www.ctan.org/pkg/algorithm2e) で、
ドキュメントの日付は algorithimx が 2005 年で
algorithm2e は 2015 年。
algorithm2e の方が新しい。

ようするに、先の引用で言われていた古いパッケージは
algorithm2e のことではなかった
(そもそも名称が違う)
んだけど、
じゃーどっち使うのがいいのよ?
ってことになる。


## 調べてみる

algorithmx と algorithm2e、
どっちがメジャーなんだろう。

TeXnician な人は「好きな方使ったらいいじゃん」っていうかもしれないけれど、
そうでない人間にとっては
自分がこれから学習するパッケージがメジャーなのかマイナーなのかは大問題だ。
せっかく覚えても、消えていく運命だったら悲しいよね。

検索してみても日本語のページは少なく、
どっちがメジャーかっていうところまではよくわからない。

StackExchange には同様の
[algorithm, algorithmic, algorithmicx, algorithm2e, algpseudocode = confused](http://tex.stackexchange.com/questions/229355/algorithm-algorithmic-algorithmicx-algorithm2e-algpseudocode-confused)
って質問が上がっている。

並んでいる回答をみると、
algorithmicx の方が文法がわかりやすいから好きって人が多そうだ。

両者のドキュメントを比較すると algorithm2e の方が機能が多そう
(マニュアルのページ数的に)。


## 結局どっちにする?

当面は文法がわかりやすいとの評判の algorithmicx にしとくのでよさそう。

algorithm2e の方が流行ってきたら乗り換えるかも。


## 参考
+ [StackExchange:algorithm, algorithmic, algorithmicx, algorithm2e, algpseudocode = confused](http://tex.stackexchange.com/questions/229355/algorithm-algorithmic-algorithmicx-algorithm2e-algpseudocode-confused)
+ [LaTeXのよくある悪習とその正しい対策](http://prml.main.ist.hokudai.ac.jp/~ryo/contents/suuri2014/suuri.pdf) (PDF)
+ [CTAN:algorithmicx](https://www.ctan.org/pkg/algorithmicx)
+ [CTAN:algorithm2e](https://www.ctan.org/pkg/algorithm2e)
