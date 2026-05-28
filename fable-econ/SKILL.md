---
name: fable-econ
description: >
  Amanda Askell's original economics fable prompt (March 2025). Picks a niche economics
  principle that early undergrads wouldn't know but late grad students would, writes a
  strict 3-paragraph illustrative story without naming the principle, then reveals and
  explains it in a single closing paragraph. Faithful to the original structured format.
  Use when user says /fable-econ, /經濟寓言, "economics fable", "經濟學寓言", or wants
  to explore economics concepts through Amanda Askell's original 3+1 story format. For
  general-purpose fable exploration across any field, use fable-explore instead.
version: "1.0.0"
user_invocable: true
---

# Fable Econ — 經濟學寓言（Amanda Askell 原版）

> 來源：Amanda Askell，2025 年 3 月 9 日 X 貼文
> https://x.com/AmandaAskell/status/1898862564718923837

這是 Amanda Askell 最初分享的提示詞，專為經濟學設計，結構明確：3 段故事 + 1 段揭曉。

## 快速用法

```
/fable-econ              ← 預設：一篇經濟學寓言
/fable-econ 國際貿易      ← 指定子領域
/fable-econ -3           ← 連續 3 篇不同子領域
/fable-econ -i           ← 互動：先讀再猜
```

## Amanda 的原版提示詞

以下是她公開的完整提示詞，本技能的行為必須忠實重現它：

> "Try to identify a somewhat niche principle or idea from the discipline of economics.
> This should be a principle or idea that early undergraduates wouldn't have heard of
> but late graduate students would have. It should be a relatively obscure but interesting
> and useful to know about nonetheless. Once you have identified such a principle, think
> of a story that could be used to illustrate your chosen principle. This should be an
> illustrative **3 paragraph story** that would fully explain the principle or idea you've
> chosen but without naming the principle or idea itself. You can then name the principle
> or idea at the end of the story and explain it and how it is illustrated by the story
> in a **single paragraph**."

## 執行規則

### 概念選擇

- **領域**：經濟學（除非使用者指定子領域如「行為經濟學」「國際貿易」「貨幣理論」）
- **難度**：大學部低年級沒聽過，但研究所後期會接觸到
- **品味**：小眾但有趣、值得知道（niche but interesting and useful）
- **避免老生常談**：不要選供需曲線、比較優勢、邊際效用這種入門概念
- Amanda 的範例故事選的是 Alchian-Allen theorem（運輸成本使高品質商品更可能被出口），這個難度和趣味性是標竿

### 故事格式（嚴格 3 段）

這是原版最關鍵的約束——不是 5 段、不是 2 段，剛好 3 段：

- **第一段**：建立場景和角色，開始呈現現象
- **第二段**：深入展開，讓讀者感受到某種經濟直覺在運作
- **第三段**：推向結局，讓讀者幾乎可以自己猜到背後的原理

全程不提任何經濟學術語或原理名稱。故事要具體、有細節、像真實發生的事。

### 揭曉格式（嚴格 1 段）

故事之後，用 `---` 分隔，寫一個段落：
1. 點出原理的正式名稱（中英文）
2. 簡要解釋它是什麼
3. 說明故事如何呈現這個概念

這一段要精煉——一個段落，不是三個。

### 完整輸出結構

```
[第一段：場景建立]

[第二段：深入展開]

[第三段：推向結局]

---

[揭曉段落：原理名稱 + 解釋 + 故事對映]
```

## 模式

| Flag | 說明 |
|------|------|
| （無） | 單篇經濟學寓言 |
| `-3` | 連續 3 篇，來自不同經濟學子領域 |
| `-i` | 互動：只寫故事，等使用者猜完再揭曉 |
| 子領域名 | 限定在特定經濟學分支（如「拍賣理論」「公共財」「勞動經濟學」）|

### 互動模式（`-i`）

寫完 3 段故事後，停在：

```
---
你覺得這個故事在講經濟學裡的什麼原理？猜猜看。
```

等使用者回覆後再揭曉。

## 輸出語言

- 預設繁體中文
- 使用者用英文就用英文

## 與 fable-explore 的差異

| | fable-econ | fable-explore |
|--|-----------|---------------|
| 領域 | 僅經濟學 | 任何領域 |
| 結構 | 嚴格 3 段故事 + 1 段揭曉 | 自由長度寓言 + 解釋段 |
| 來源 | 2025.3 原版提示詞 | 2026.4 訪談通用版 |
| 風格選項 | 無 | 莊子/科幻/伊索/偵探 |
| 跨域模式 | 無 | 有（`-x`）|

想跨領域探索用 `/fable`，想專注經濟學用 `/fable-econ`。
