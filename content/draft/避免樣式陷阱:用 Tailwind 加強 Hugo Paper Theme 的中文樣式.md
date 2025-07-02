---
date: '2025-07-01T12:30:54+08:00'
draft: true
hidemeta: false
comments: false
tags: ["first"]
cover:
    image: "<image URL>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
title: '避免樣式陷阱:用 Tailwind 加強 Hugo Paper Theme 的中文樣式'
description: "Desc Text."
canonicalURL: 'https://https://minalogs.github.io/post/避免樣式陷阱:用 Tailwind 加強 Hugo Paper Theme 的中文樣式'
author: "Mina Liu"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
# 日常：願景 → 拆解 → 反饋
# 職能：【創辦人視角 - 問題與價值】 → 【PM視角 - 問題拆解與解決方案】 → 【HR視角 - 成果與效益】
---

非常好，Mina，你一次提出了兩個層次的思考：

1. **一個深度內在轉變的文章主題**（你會之後補充再寫）
2. **一個針對 Hugo + Tailwind 樣式強化的技術策略問題**

我這邊先回應 **第二個技術主題**，幫你梳理思路，避免你又掉入「不必要的樣式地獄」，同時提供一個**最輕巧又穩定可維護的 Tailwind 佈局增強策略**，再搭配 Hugo 的 `i18n` 限定中文使用的技巧。

---

## 🛠 主題：用 Tailwind 加強 Hugo Paper Theme 的中文樣式，但避免樣式陷阱

---

### ✅ 你的需求可以整理成這四點：

| 需求                   | 目的                     | 限制與焦慮          |
| -------------------- | ---------------------- | -------------- |
| 加入 Tailwind          | 讓樣式更直觀、可重用、避免 style 雜亂 | 不想用太重的框架、怕過度魔改 |
| 只針對中文做版面優化           | 中文排版醜，需要改善閱讀感受         | 不能影響英文版本       |
| 減少文章圖片               | 讓人專注文字內容               | 也代表字體與留白更重要    |
| 保留 Hugo Paper 的原本簡潔感 | 不想動太多主題底層結構            | 避免未來維護困難       |

---

### ✅ 建議方案概述：

> **只針對 `zh` 語系的頁面注入 Tailwind，並用自訂 `prose` 樣式優化中文排版，不重寫整個樣式，不動 base theme**

---

## ✅ 實作策略

### 1️⃣ 加入 Tailwind（開發版即可）

Paper 是純 HTML/CSS 主題，可以直接新增 `assets/css/tailwind.css`，內容如下：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

然後在 `config.toml` 開啟 Hugo 的 [Hugo Pipes](https://gohugo.io/hugo-pipes/) 支援：

```toml
[params]
  tailwind = true
```

或在 `head.html` 內條件加載：

```html
{{ if eq .Site.Language.Lang "zh" }}
  <link rel="stylesheet" href="{{ "css/tailwind-generated.css" | resources.Get | resources.Minify | resources.Fingerprint | resources.RelPermalink }}">
{{ end }}
```

你可以用 `i18n` 條件控制只在 `zh` 頁面引入 Tailwind。

---

### 2️⃣ 使用 Tailwind 的 `prose` + 中文優化類別

建議針對內容區域使用自定義的 `prose-zh` 類別，設定在 Tailwind config：

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      typography: ({ theme }) => ({
        zh: {
          css: {
            fontFamily: 'Noto Serif TC, serif',
            lineHeight: '2',
            letterSpacing: '0.03em',
            p: {
              marginTop: theme('spacing.4'),
              marginBottom: theme('spacing.4'),
            },
            h1: { fontWeight: '700' },
            h2: { fontWeight: '600' },
            blockquote: {
              fontStyle: 'normal',
              borderLeft: `4px solid ${theme('colors.gray.300')}`,
              paddingLeft: theme('spacing.4'),
            },
          },
        },
      }),
    },
  },
  plugins: [require('@tailwindcss/typography')],
}
```

然後在你文章模板中，只有中文版本的文章 layout 用：

```html
<article class="prose prose-zh">
  {{ .Content }}
</article>
```

---

### 3️⃣ 限定 Tailwind 只出現在 `zh` 頁面

在 `layouts/_default/baseof.html` 加條件：

```html
{{ if eq .Site.Language.Lang "zh" }}
  <link rel="stylesheet" href="{{ "css/tailwind.css" | resources.Get | resources.PostCSS | resources.Minify | resources.Fingerprint | resources.RelPermalink }}">
{{ end }}
```

也就是說，英文頁面完全不會加載 tailwind，不會汙染樣式與載入效能。

---

### 4️⃣ 關於圖片使用的建議

既然你希望讓讀者**更專注文字內容**，這會讓排版、留白與段落層次的視覺效果更重要，圖片就改為：

* ❌ 不用配圖營造氣氛
* ✅ 使用小小的 divider 圖案或細線來區隔章節（也可以用 Tailwind class）
* ✅ 或僅在封面放一張人物背影、色塊主視覺，內文全文字

---

## ✨ 總結這套策略的好處：

| 條件                                        | 結果                       |
| ----------------------------------------- | ------------------------ |
| Tailwind 僅在 zh 頁面啟動                       | 保持英文版載入輕巧                |
| 使用 `@tailwindcss/typography` + `prose-zh` | 中文排版不再擁擠、醜陋或對齊不順         |
| 樣式全部用 class 寫                             | 不污染原有樣式結構、不需要進 style tag |
| 動態調整圖片使用方式                                | 強化文字表達焦點，而非靠圖吸引注意        |

---

我可以幫你生成一個初始的 Tailwind config、中文專用 `prose-zh.css` 結構、以及 `.html` 模板的調整範例，這樣你就不用自己拆太多。你想要我幫你做這一步嗎？或者你想直接用你目前現有的 Hugo 專案來套進去？
