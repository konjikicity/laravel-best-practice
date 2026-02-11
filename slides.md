---
theme: nord
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
download: true
colorSchema: 'dark'
layout: center
highlighter: shiki
title: 'Laravelベストプラクティス勉強会'
lineNumbers: true
transition: slide-left
mdc: true
fonts:
  # basically the text
  sans: 'Noto Sans JP'
  # use with `font-serif` css class from windicss
  serif: 'Robot Slab'
  # for code blocks, inline code, etc.
  mono: 'Fira Code'
---

<h1 class="font-bold">Laravelベストプラクティス勉強会</h1>

<p claas="pt-12">
受託ナレッジシェア by Ond
</p>

---
transition: fade-out
---

<h1 class="font-bold">
    目次
</h1>

<div class="pt-6"></div>

<p class="font-bold">
1. PHPとは？
</p>

<div class="pt-6"></div>

<p class="font-bold">
2. Laravelとは？
</p>

<div class="pt-6"></div>

<p class="font-bold">
3. Laravelベストプラクティス
</p>

<div class="pt-6"></div>

<p class="font-bold">
4. 個人的に大事だと思うこと
</p>

<div class="pt-6"></div>

<p class="font-bold">
5. おわりに
</p>

---
transition: fade-out
---

<h1 class="font-bold">
今日のテーマ
</h1>

<h2 class="py-12 font-bold text-red">やっぱり「進研ゼミ」</h2>

<h3>
    ちょっとでも記憶に残ってくれたら嬉しいです。
</h3>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">PHPとは</h1>

---
transition: fade-out
---

<h1 class="font-bold">PHP</h1>

<h2 class="py-12 font-bold text-red">基本的にはブラウザ上で動作するプログラミング言語</h2>

HTML・CSSに追加して使用することで<br>
Webページを動的にすることができる

<p class="pt-12">例) モーダル表示・拡大表示etc...</p>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">Laravelとは</h1>

---
transition: fade-out
---

<h1 class="font-bold">Laravel</h1>

<h2 class="py-12 font-bold text-red">基本的にはブラウザ上で動作するプログラミング言語</h2>

HTML・CSSに追加して使用することで<br>
Webページを動的にすることができる

<p class="pt-12">例) モーダル表示・拡大表示etc...</p>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">Laravelベストプラクティス</h1>

---
transition: fade-out
---

<h1 class="font-bold">アロー関数</h1>

<div class="flex">
<div class="w-1/2 p-2">

<h3 class="font-bold text-red py-6">無名関数</h3>

```javascript
// 宣言した時点では名前がないので無名関数と呼ばれる。
const onodaBMI = function(weight,height){
 return weight + height * 2;
};

```
</div>

<div class="w-1/2 p-2">
<h3 class="font-bold text-red py-6">アロー関数</h3>

```javascript
// アロー関数とは無名関数をシンプルに書けるやつ
const onodaBMI = (weight, height) => {
  return weight + height * 2;
};

// 1. 1文で終わる時は{}を省略可能
const onodaBMI = (weight, height) => weight + height * 2;

// 2. 引数が一個の時は()を省略可能
const onodaBMI = weight => weight + 172 * 2;

// 3. 引数がないときは()を省略せずに書く
const onodaBMI = () => weight + 172 * 2;

```
</div>
</div>
---
transition: fade-out
layout: center
---

<h1 class="font-bold">
    おわりに
</h1>

---
transition: fade-out
---

<h1 class="font-bold">おわりに</h1>
<div>

<p class="pt-6">結構なボリュームになってしまいましたが、</p>
<p>今回の発表で再度、自分も復習できました。</p>
<p>JavaScriptは奥が深く、今回紹介したのも一部ですが、</p>
<p>構文等はよく出てくるものをチョイスしたので、ためになれば幸いです。</p>

<h3 class="text-red pt-12">あとslidevで作るのはすごく楽しかったです。</h3>
<p class="text-blue">https://ja.sli.dev</p>
</div>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">ご静聴ありがとうございました。</h1>
