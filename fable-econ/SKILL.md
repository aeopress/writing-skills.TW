---
name: fable-econ
description: >
  Amanda Askell's original economics fable prompt (March 2025). Picks a niche economics
  principle that early undergrads wouldn't know but late grad students would, writes a
  strict 3-paragraph illustrative story without naming the principle, then reveals and
  explains it in a single closing paragraph. Faithful to the original structured format.
  Use this skill whenever the user says /fable-econ, /經濟寓言, "economics fable",
  「經濟學寓言」, "illustrate an economics concept", or asks for a story that teaches
  an economics idea — even when they don't explicitly say "fable" but want to learn
  an economics concept through narrative. For general-purpose fable exploration across
  any field, use fable-explore instead.
version: "1.4.0"
user-invocable: true
argument-hint: "[子領域，如 拍賣理論] [-3 連續三篇] [-i 互動猜題]"
---

# Fable Econ — 經濟學寓言（Amanda Askell 原版）

> 來源：Amanda Askell，2025 年 3 月 9 日 X 貼文
> https://x.com/AmandaAskell/status/1898862564718923837

Amanda 最初分享的提示詞，**鎖定經濟學**、結構嚴格：3 段故事 + 1 段揭曉。

## 為什麼是這個結構

3 段是經過設計的。每段在故事中扮演一個經濟學論證步驟，3 段湊起來就完整呈現了一個原理的因果鏈。少於 3 段不夠展開，多於 3 段稀釋強度。

揭曉用 1 段，**不是 3 段**——讀者已經在故事裡「活過」這個原理了，揭曉段是命名與點題，不是重新解釋。

## 快速用法

```
/fable-econ              ← 預設：一篇經濟學寓言
/fable-econ 國際貿易      ← 指定子領域
/fable-econ -3           ← 連續 3 篇不同子領域（擴充模式，非原版）
/fable-econ -i           ← 互動：先讀再猜（擴充模式，非原版）
```

## Amanda 的原版提示詞

以下是她公開的完整提示詞，本技能必須忠實重現它：

> "Try to identify a somewhat niche principle or idea from the discipline of economics.
> This should be a principle or idea that early undergraduates wouldn't have heard of
> but late graduate students would have. It should be a relatively obscure but interesting
> and useful to know about nonetheless. Once you have identified such a principle, think
> of a story that could be used to illustrate your chosen principle. This should be an
> illustrative **3 paragraph story** that would fully explain the principle or idea you've
> chosen but without naming the principle or idea itself. You can then name the principle
> or idea at the end of the story and explain it and how it is illustrated by the story
> in a **single paragraph**."

## 概念選擇

- **領域**：經濟學（除非使用者指定子領域如「行為經濟學」「國際貿易」「貨幣理論」）
- **難度**：大學部低年級沒聽過，研究所後期會接觸到
- **品味**：小眾但有趣、值得知道
- **真實性檢核**：選的原理必須是真實學術用語，能在維基百科或標準教科書查到——**不要編造**
- **避免老生常談**：不要選供需曲線、比較優勢、邊際效用、機會成本、囚徒困境這些入門概念
- **可類比優於最炫**：研究所概念有很多，**有強日常類比的優先選**。「Alchian-Allen 用蘋果」「贏家詛咒用油田競標」這種有清楚物理對應的，比「Tinbergen 政策不可能性定理」這種純抽象的更適合寓言
- **標竿難度**：Amanda 原版範例選的是 **Alchian-Allen theorem**（運輸成本使高品質商品更可能被出口）。這個難度和趣味性就是品味標準

可選範圍舉例（給靈感，不是清單）：
Alchian-Allen theorem、Veblen good、Giffen good、winner's curse、Akerlof's lemon problem、Tinbergen rule、Goodhart's law、Baumol cost disease、Allais paradox、Diamond-Mortensen-Pissarides search、Coase theorem 的反例、ratchet effect、grim trigger、principal-agent 的多任務問題、bandit-arm exploration vs exploitation、Tiebout sorting、network externality with tipping point、informational cascade、Bertrand-Edgeworth 循環、Hotelling 海灘模型的退化、ad valorem vs specific tax 等價性失效、Jensen's inequality 在投資組合的後果。

## 段落結構（具體拆解）

不要用「建立場景／深入展開／推向結局」這種抽象詞——那會寫出平板段落。每段有具體任務：

### § 1 ─ 建立基線

讓讀者看見「正常的世界」運作邏輯。引入主角、地點、兩種以上可比較的選項或狀態、本地或初始的相對價格／偏好／行為。讀者讀完應該心裡有個「這世界是這樣運作的」基準感。

### § 2 ─ 引入摩擦或變因

加入一個固定成本、距離、規則、訊息缺口、時間延遲、配額——任何會改變相對結構的東西。**這裡是原理開始發力的地方**。讀者讀完應該隱約感覺「咦，事情好像會變得不一樣」，但還沒看見結果。

### § 3 ─ 揭露反轉

呈現結果，最好是反直覺的對比——遠地 vs 本地、長期 vs 短期、預期 vs 實際。讀者讀完應該幾乎可以自己猜到背後的原理是什麼，即使說不出名字。

**全程不提任何經濟學術語或原理名稱**。故事要具體、有細節、像真實發生的事。

## 揭曉段（嚴格 1 段）

故事後用 `---` 分隔，**一個段落**完成這些事：

1. 點出原理的正式名稱（中英文皆列）
2. 簡要解釋它是什麼（1-2 句）
3. 說明故事中哪些元素對應原理的哪個部分
4. 可選：補一句反直覺結論或現實應用

一段——不是三段、不是條列。**直白優先**：用日常語言解釋機制，能講白就講白，不要為了學術感堆術語。

**直白 ≠ 提早揭。揭曉時機優先：把白話留到揭曉段，故事不要解釋機制**。如果你忍不住想在故事裡讓某個角色（老師、會計、導師）開口解釋原理是怎麼運作的——停下。那段話應該搬到揭曉段。故事要呈現「發生了什麼」，揭曉段才負責「為什麼」。

## 可理解性測試（寫完後做）

寫完整篇後，問自己這兩件事：

1. **沒讀過經濟學的朋友能不能讀懂故事本身？** 不能 → 故事太抽象或數字太亂，重寫。
2. **讀完揭曉段，這位朋友能不能用自己的話複述這個原理是什麼？** 不能 → 揭曉段堆術語了，改成更直白的講法。

通不過就改。寓言的價值在「讀完世界跟之前不一樣」，看不懂就什麼都沒留下。

## 完整輸出結構

```
[第一段：建立基線]

[第二段：引入摩擦]

[第三段：揭露反轉]

---

[揭曉段：原理名稱 + 解釋 + 故事對映]
```

---

## 標竿範例（Amanda 原版產出）

這是 Amanda 用她自己的 prompt 跑出來的範例。**整篇就是品質與結構的範本**——以後寫每一篇都應該對齊這個標竿。

> ### The Curious Case of Oakridge Orchards
>
> Maria owned an apple orchard in Oakridge Valley where she grew two varieties of apples: standard and premium. The standard apples sold locally for $2 per pound, while the premium apples—sweeter, crisper, and more visually appealing—sold for $4 per pound. Local customers generally purchased more standard apples than premium ones, as many felt the premium apples weren't worth twice the price despite their superior quality.
>
> When a distributor from a city 2,000 miles away approached Maria about selling her apples there, she was excited but concerned about transportation costs. The distributor explained that shipping would add a flat $3 per pound to both varieties. Maria did the math: her standard apples would now cost $5 in the distant city, while premium apples would cost $7. The price ratio had changed—premium apples were now only 40% more expensive than standard ones, rather than 100% more expensive as they were locally.
>
> Six months later, Maria was surprised when visiting the distant city to find her premium apples prominently displayed in high-end markets, selling briskly despite the higher absolute price. Meanwhile, back in Oakridge, locals began complaining that most premium apples were being shipped away. The orchard's books confirmed this trend: in the local market, standard apples outsold premium ones by 3-to-1, but in the distant city, premium apples outsold standard ones by 2-to-1.
>
> ---
>
> This story illustrates the **Alchian-Allen theorem**, sometimes called the "shipping the good apples out" effect. When a fixed per-unit cost (like transportation) is added to both high and low-quality versions of a product, it changes their relative prices, making the higher quality version relatively less expensive compared to the lower quality one. As a result, consumers in distant markets tend to buy proportionally more high-quality goods, while local markets are left with more lower-quality options—a counter-intuitive effect that explains why producing regions often export their best products while consuming more of their standard ones locally.

### 範例段落拆解（注意每段在做什麼）

- **§ 1（建立基線）**：兩個品質、本地價差、本地偏好——讀者看見「Oakridge 的正常世界」
- **§ 2（引入摩擦）**：固定運費 $3 進來，價差比從 100% 變 40%——讀者開始隱約感覺結構變了
- **§ 3（揭露反轉）**：六個月後的觀察：遠地高端市場熱賣、本地反而抱怨買不到、3:1 vs 2:1 的對比——反直覺結論浮現
- **揭曉段**：點名 Alchian-Allen theorem → 機制（固定成本壓縮相對價差）→ 對映（運費、價比、購買比例）→ 應用洞察（為什麼產地常吃較差的）

整篇沒有出現「邊際」「彈性」「相對價格」這些經濟學詞——讀者讀完卻完全理解了相對價格機制。**這是寓言的力量，不是教科書的力量**。

---

## 常見失敗模式（不要這樣寫）

- ❌ **過早暗示原理**：第一段就出現「相對價格」「彈性」這種詞
- ❌ **教科書比喻語氣**：「假設有一個經濟人……這就像 XX 理論一樣……」
- ❌ **段落空洞**：「Maria 經營果園，賣蘋果。她遇到了一個有趣的問題。她解決了它。」——沒有具體數字、沒有對比、沒有結構
- ❌ **揭曉段灌水**：把揭曉段寫成 3 段教學文。揭曉一段就夠——故事已經做了大部分工作
- ❌ **選太基礎的原理**：供需曲線、機會成本、比較優勢——這些大學部一年級就學過
- ❌ **編造原理**：寫一個聽起來像但其實不存在的「Smith-Jones theorem」
- ❌ **角色名重複**：每次都用「Maria」「Chen」這種預設名——換地點、換職業、換背景
- ❌ **抽象到無畫面**：「一個經濟體中存在兩種商品……」——這不是故事，是練習題
- ❌ **數字／距離／價格邏輯不一致**：寫完後**自己用算術驗一遍**。例如「陳伯在 km 5、新店在 km 2，住 km 4 的人會去新店」——錯，到陳伯 1 km、到新店 2 km。又如「兩家最後都在 km 3 與 km 4，相距 300 公尺」——錯，1 km。LLM 為了具體常隨手丟數字，但每個數字都會被讀者驗算
- ❌ **故事前加 metadata header**：**輸出的第一個字元就是故事的第一個字**。不要加 `# 經濟學寓言：XXX`、`**概念：YYY**`、`題目：ZZZ`、`領域：行為經濟學`、作者署名、章節標籤——任何放在 `[第一段]` 之前的東西都會破壞揭曉懸念。直接從第一句故事文字開始
- ❌ **領域子分類／格式名稱也算術語**：「拍賣理論」題目下，「英式拍賣」「荷蘭式拍賣」「維克瑞拍賣」「密封第一價格」也是學術名稱，故事段一樣不能出現；改用行為描述（如「一口一口往上加」「從高往低降，第一個喊停的得標」「各人在紙條上寫數字，封進信封」）。其他子領域同理：「囚徒困境」「納許均衡」「卡尼曼-特沃斯基」等都算

## 模式（擴充）

| Flag | 說明 | 是否原版 |
|------|------|---------|
| （無） | 單篇經濟學寓言 | ✅ 原版 |
| 子領域名 | 限定在特定經濟學分支（如「拍賣理論」「公共財」「勞動經濟學」） | ✅ 相容原版 |
| `-3` | 連續 3 篇，來自不同經濟學子領域 | ⚠️ 擴充（原版是 pick one） |
| `-i` | 互動：只寫故事，等使用者猜完再揭曉 | ⚠️ 擴充 |

### 互動模式（`-i`）

寫完 3 段故事後，停在：

```
---
你覺得這個故事在講經濟學裡的什麼原理？猜猜看。
```

等使用者回覆後再揭曉。

## 輸出語言

- 預設繁體中文（台灣用語），故事內人名地名可中可英，看故事自然度而定
- 使用者用英文下指令就用英文寫
- 揭曉段的原理名稱中英文並列（中文翻譯不確定時以英文為主）

## 與 fable-explore 的差異

| | fable-econ | fable-explore |
|--|-----------|---------------|
| 領域 | 僅經濟學 | 任何領域 |
| 結構 | 嚴格 3 段故事 + 1 段揭曉 | 自由長度寓言 + 解釋段 |
| 來源 | 2025.3 原版 Twitter 提示詞 | 2026.4 Newcomer Pod 訪談通用版 |
| 風格選項 | 無 | 莊子／科幻／伊索／偵探 |
| 跨域模式 | 無 | 有（`-x`） |
| 難度級距 | 固定（研究所） | 國小～專家可調 |

想跨領域探索用 `/fable-explore`，想專注經濟學、要嚴謹 3+1 結構用 `/fable-econ`。
