---
title: "Popover API × anchor-position × @starting-style で作るTooltip"
emoji: "🔭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [HTML,CSS,JavaScript,React]
published: false
---

## はじめに

こんにちは！😄
みなさん、少し前ですがCSS Wrapped 2024はチェックされましたか？私はCSS Wrapped 2024を見て、「おぉ…この辺の技術で何か作って試してみたい！」となりまして、何かちょうどさくっと作れるいい題材がないかな…と考えた結果、「Popover API」「anchor-position」「@starting-style」を使ってTooltipを作ることにしました！

というわけで、色々な技術を試したいエンジニアの好奇心のままに、CSS Wrapped 2024で紹介された技術を駆使したTooltipのご紹介をします🚀

https://chrome.dev/css-wrapped-2024/

## まずはそれぞれの技術の概要から

:::message alert

執筆時(2025年2月)は、「[anchor-position](https://caniuse.com/css-anchor-positioning)」について一部のブラウザが対応していないため、ご注意ください。

:::

「Popover API」「anchor-position」「@starting-style」を使ってTooltipを作っていきますが、いきなり実装に入る前に、それぞれが**何をするものなのか**を簡単に説明しておきます。

### Popover APIとは

https://developer.mozilla.org/ja/docs/Web/API/Popover_API

Popover APIは**JavaScriptを使用せずにポップオーバー要素を簡単に表示できるAPI**になります。

これまではCSSの`position:absolute`と`z-index`を駆使し、かつ「ポップオーバー要素の表示時は、ポップオーバー以外の要素を押すとポップオーバー要素を非表示にする」といった制御をJavaScript側で行い、考慮しなければいけない点が多く実装が大変でした。しかし、Popover APIの登場によりブラウザが自動的に開閉を管理してくれるため、実装が圧倒的にシンプルになります。

### anchor-positionとは

https://developer.mozilla.org/ja/docs/Web/CSS/CSS_anchor_positioning/Using

anchor-positionは、要素の配置を制御する新しい仕様になります。

これまではJavaScriptを用いて要素の座標や大きさを計算し、それらを元に要素を配置していましたが、**anchor-positionを使うことでCSSだけでこの計算が可能**になります。また、要素を特定の位置に直感的に配置できるようになります。

### @starting-styleとは

https://developer.mozilla.org/ja/docs/Web/CSS/@starting-style

@starting-styleは**トランジションの開始時のスタイルを定義するためのアットルール**になります。

これまでは`display: none`の要素にはトランジションが適用されませんでした。(私も何度もこの罠に引っかかりました...) @starting-styleを使用することで、トランジション開始時のスタイルを指定することができ、トランジションを行うことができます。

## <実装編>Tooltipを作成する

完成したTooltipおよびコードは以下の通りになります。それでは解説していきましょう！

![](https://storage.googleapis.com/zenn-user-upload/07a399f8263a-20250201.gif)

```tsx
import "./style.css";
import { UserIcon } from "./UserIcon";

const POPOVER_TARGET = "popoverTarget";

export const Index = () => {
  return (
    <div>
      <button
        className="user-button"
        {...{
          popovertarget: POPOVER_TARGET,
          popovertargetaction: "show",
        }}
      >
        <UserIcon />
      </button>
      <div
        id={POPOVER_TARGET}
        className="tooltip"
        {...{
          popover: "auto",
        }}
      >
        ToolTip
      </div>
    </div>
  );
};
```

```css
.user-button {
  width: 80px;
  aspect-ratio: 1;
  padding: 12px;
  background-color: #b0e8b8;
  border: none;
  border-radius: 999px;
  anchor-name: --user-button;
}

.tooltip {
  position: relative;
  position-anchor: --user-button;
  align-self: anchor-center;
  left: calc(anchor(right) + 12px);
  padding-block: 8px;
  padding-inline: 12px;
  margin: 0;
  overflow: visible;
  color: #fff;
  background-color: #000;
  border: none;
  border-radius: 8px;
  opacity: 0;
  transition: 0.2s ease-out;
  transition-behavior: allow-discrete;
}

.tooltip::before {
  position: absolute;
  top: 0;
  bottom: 0;
  left: -10px;
  width: 12px;
  height: 15px;
  margin: auto;
  content: "";
  background-color: #000;
  clip-path: polygon(0% 50%, 100% 0%, 100% 100%);
}

[popover]:popover-open {
  opacity: 1;

  @starting-style {
    opacity: 0;
  }
}
```

### Popover APIでTooltipの表示・非表示を制御する

:::message alert

`popover`属性を直接JSXに記述しようとすると、ReactDOMが対応しておらず警告が出るため、オブジェクトを利用しています。しかし、以下のPRで対応されたようなので、直にオブジェクトに展開せずともHTMLライクに書けるようになりそうです！

https://github.com/facebook/react/pull/27981

:::

#### ポップオーバーのコントロール要素

ポップローバー要素の表示／非表示を行うコントロール要素を説明します。(今回は`<button/>`がコントロール要素になります。)

コントロール要素に、`popovertarget`属性と`popovertargetaction`属性を付与します。`popovertarget`属性は、ポップオーバー要素と紐付けを行うために、ポップオーバー要素の`id`と同じ値を指定します。そして`popovertargetaction`属性に`show`を指定することでボタン押下時に、ポップオーバー要素を表示します。

```tsx
<button {...{popovertarget: POPOVER_TARGET, popovertargetaction: "show",}}>
    <UserIcon />
</button>
```

#### ポップオーバー要素

ポップオーバー要素については、コントロール要素で指定した`popovertarget`属性と同じ値を`id`に指定します。また、対象がポップオーバー要素であることを指定するために`popover`属性を付与します。

```tsx
<div id={POPOVER_TARGET} {...{popover: "auto",}}>
```

これだけです！！コントロール要素とポップオーバー要素にそれぞれ属性を付与するだけで簡単に実現可能になりました。もちろん、**他の要素をクリックした場合などは、ポップオーバー要素は非表示**になります。

### anchor-positionでTooltipを配置する

今回作成したTooltipは`.user-button`の中央右横に配置しています。

1. アンカーとなる`.user-button`に`anchor-name: --user-button;`を指定する。
2. `.tooltip`に`position-anchor: --user-button;`を指定して、アンカーの`.user-button`と紐付けを行い、`.user-button`を基準として`.tooltip`を配置するようにする。
3. `align-self: anchor-center;`で`.user-button`の中央と`.tooltip`の中央を揃える。
4. `left: calc(anchor(right) + 12px);`で`.user-button`の右側から`12px`分離れた位置に配置する。(`12px`は吹き出し分の大きさであり、`calc`で計算できます。)

![](https://storage.googleapis.com/zenn-user-upload/0d2992cc881e-20250202.png)

```css
.user-button {
  anchor-name: --user-button;
}

.tooltip {
  position-anchor: --user-button;
  align-self: anchor-center;
  left: calc(anchor(right) + 12px);
}
```

いかがでしょう？とても直感的にToolTipを配置することができました！初めてanchor-positionを使いましたが、これはすごく便利かつ、直感的で最高だ...となりました。

### @starting-styleでTooltipにアニメーションをつける

Popover APIについて`popover`属性を付与した要素は、非表示(`display: none`)になります。この`display: none`の要素に対してアニメーションをするために@starting-styleを使用します。

使い方は簡単で@starting-styleで開始時のアニメーションを指定するだけです。

```css
[popover]:popover-open {
  opacity: 1;

  @starting-style {
    opacity: 0;
  }
}
```

こちらもとても簡単に実装することができました！@starting-styleは`<dialog/>`などでも使用することができるので、これから活躍の場が増えてきそうですね。

## まとめ

「Popover API」「anchor-position」「@starting-style」を使ってTooltipを作成しました。

お気づきの方もいるかもしれませんが、今回の実装ではJavaScriptでコネコネしている部分はなくJSX部分＋CSSだけで完結しています。これまでは、座標計算やイベント制御などをJavaScriptでゴリゴリ書くのが一般的でしたが、今やWeb APIやCSSの進化によって、こうしたUIの挙動もシンプルに実現できるようになりました。

最近はWeb APIやCSSでできることが増えてきて、JavaScriptの役割が少しずつ減ってきているなあ…と改めて感じました。ちなみに、Chrome133で`popover=hint`属性も新たに追加予定のため要チェックですね！！これからも、こうした技術を活用しながらよりシンプルで洗練された実装を目指していきたいです！

最後までお読みいただき、ありがとうございました！🙇