---
title: "Popover API × anchor-position × @starting-style で作るモダなTooltip"
emoji: "🏺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [HTML,CSS,JavaScript,React]
published: false
---

## はじめに

こんにちは！
みなさん、少し前ですがCSS Wrapped 2024はチェックされましたか？私はCSS Wrapped 2024を見て、「おぉ…この辺の技術で何か作って試してみたい！」となりまして、それで何かちょうどさくっと作れるいい題材がないかな…と考えた結果、「Popover API」「anchor-position」「@starting-style」を使ってTooltip を作ることにしました！

というわけで、新しい技術を試したいエンジニアの好奇心のままに、CSS Wrapped 2024 で紹介された技術を駆使したTooltipのご紹介をします🚀

https://chrome.dev/css-wrapped-2024/

## まずはそれぞれの技術の概要から

「Popover API」「anchor-position」「@starting-style」 の3つの技術を使って Tooltipを作っていきますが、いきなり実装に入る前に、それぞれが**何をするものなのか**を簡単に説明しておきます。

### Popover APIとは

https://developer.mozilla.org/ja/docs/Web/API/Popover_API

Popover APIは簡潔に言うと**JavaScriptを使用せずにポップオーバー要素を簡単に表示できるAPI**になります。

これまではCSSの`position:absolute`と`z-index`を駆使し、かつ「ポップオーバー要素が表示時は、ポップオーバー以外の要素を押すとポップオーバー要素を非表示にする」といった制御をJavaScript側で行い、考慮しなければいけない点が多く実装が大変でした。しかし、Popover APIの登場によりポップオーバー要素の表示・非表示の実装・制御がとてもシンプルになります。

### anchor-positionとは

https://developer.mozilla.org/ja/docs/Web/CSS/CSS_anchor_positioning/Using

anchor-positionは、要素の配置を制御する新しい仕様になります。

これまではJavaScriptを用いて要素の座標や大きさを計算し、それらを元に要素を配置していましたが、**anchor-positionを使うことでCSSだけでこの計算が可能**になります。また要素の配置もこの要素のこの位置に配置するといった指定も直感的に実装することができます。

### @starting-styleとは

https://developer.mozilla.org/ja/docs/Web/CSS/@starting-style

@starting-styleはトランジションの開始時のスタイルを定義するためのアットルールになります。

これまでは`display: none`の要素に対してトランジションを使用した場合、トランジションが発生しませんでした。(私も何度もこの罠に引っかかりました。)@starting-styleを使用し、トランジション開始時のスタイルを指定することができ、正しくトランジションを行うことができます。

## <実装編>Tooltipを作成する



## まとめ
