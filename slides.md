---
theme: seriph 
background: https://source.unsplash.com/collection/94734566/1920x1080
class: 'text-center'
highlighter: shiki
lineNumbers: false
info: |
  ## Shadow DOMのImperative Slotting APIを試す
---

# Shadow DOMの<br>Imperative Slotting APIを試す

---

# 自己紹介

* 衣笠和樹
* 株式会社シーズ
* Webプログラマ
* 仕事ではPHP, js触ってます
* Web Componentsに頑張ってほしい

---

# Imperative Slotting API

* ブラウザで使えるWeb標準API
* Shadow DOMで仕様される`<slot>`要素をjsで制御するAPI
* 仕様はHTML Living Standardに記載されている
* 最近追加された
* 今のところChromeとEdgeで動く

---

# Shadow DOM

<div grid="~ cols-2 gap-4">
<div>

* Web Componentsの仕様のひとつ
* カプセル化したDOMツリーを作成する
* スタイルや挙動の影響範囲を小さくできる
* ホスト要素の子要素のように描画される
* Custem Elementsと一緒に使う

</div>
<div>

```html {all|5-13|18-20}
<script>
  class HostElement extends HTMLElement {
    coustructor() {
      super();
      this.attachShadow({mode: "open"});
      this.shadowRoot.innerHTML = `
      <style>
        :host { display: block; }
        div { color: red; }
      </style>
      <div>Dark DOM</div>
      <slot>
      `;
    }
  }
  customElements.define('host-element', HostElement);
</script>
<host-element>
  <div>Light DOM</div>
</host-element>
```

</div>
</div>

---

# `<slot>` 要素

<div grid="~ cols-2 gap-4">
<div>

* Light DOMをShadow DOMに注入するための要素
* `<button>`や`<ul>`のような要素を作るのに使える
* Vue.js にも同じようなのがある

</div>
<div>

```html {12|19}
<script>
  class MyElement extends HTMLElement {
    coustructor() {
      super();
      this.attachShadow({mode: "open"});
      this.shadowRoot.innerHTML = `
      <style>
        :host { display: block; }
        div { color: red; }
      </style>
      <div>Dark DOM</div>
      <slot>
      `;
    }
  }
  customElements.define('my-element', MyElement);
</script>
<my-element>
  <div>Light DOM</div>
</my-element>
```

</div>
</div>

---

# `<slot>` のイケてないところ

* 複数slotの場合はname属性を付けてふりわける要素にslot属性を付ける
  * `<details>` 要素を再現できない
* **Imperative Slotting API で属性なしでできるようになった**

```html {1-4|5-16}
<details>
  <summary>概要</summary>
  いい感じの説明
</details>
<my-details>
  <div slot="summary">概要</div>
  <div slot="detail">いいかんじの説明</div>
  <!-- shadow tree
  <details>
    <summary>
      <slot name="summary"></slot>
    </smmary>
    <slot name="detail">
  </details>
  -->
</my-details>
```

---

# `<slot>` ができないこと

* Light DOM から要素を絞り込んで注入することができない
* **Imperative Slotting APでできるようになった**

```html
<tab-menu show-label="A">
  <div label="A">A</div>
  <div label="B">B</div>
  <div label="C">C</div>
  <!-- shadow tree
  <div>
    <slot> <-- label="A"のdivだけ入れて表示したい
  </div>
  -->
</tab-menu>
```

---

# Imperative Slotting API の使いかた

* `attachShadow()` のオプションに `slotAssignment: "manual"` を指定する
* slot要素の `assign()` で自分の好きなようにLight DOMを注入する

---
layout: fact
---

## デモ

https://github.com/umekobutea/tab-menu

---

# 気づいたこと

* assignできるのはあくまでそのslotのホスト要素のLight DOM
  * `<template>`から取ってきたDocumentFragmentの要素を入れるとエラーになった
  * Documentに含まれている要素でもホスト要素の子要素に含まれてなければエラー
* `<slot>` を更新するたび、他の要素も更新する場合、DOMのAPIだけだと面倒臭い
  * 今回はlit-htmlを使ったが、`<template>`をいいかんじにする案[^1]も出てるみたい

[^1]: https://github.com/WICG/webcomponents/blob/gh-pages/proposals/DOM-Parts.md

---
layout: fact 
---

<p class="text-5xl">ありがとうございました</p>

---

参考資料
* https://github.com/WICG/webcomponents/blob/gh-pages/proposals/Imperative-Shadow-DOM-Distribution-API.md
* https://html.spec.whatwg.org/multipage/scripting.html#the-slot-element
* https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements