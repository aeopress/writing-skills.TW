# writing-skills.TW

> 給 Claude Code 的繁中寫作技能：**用寓言學任何概念**。

Amanda Askell 在 X 上分享過一個她最愛的提示詞——讓 Claude 從某個學科裡挑一個小眾原理，寫成一則 3 段故事，最後一段才揭曉。她說讀這些「academic allegories」變成她取代滑社群媒體的健康儀式。一年後她在 Newcomer Pod 訪談裡又把這個玩法擴展到任何領域、任何難度。

這個 repo 把兩個版本都做成 Claude Code skill，繁中（台灣用語）優先。

---

## 一段示範

下面是用 `/fable-econ` 跑出來的一篇——一個經濟學原理，藏在台北東區兩家麵館的故事裡。讀完故事再看揭曉，是這個 skill 的設計核心。

> 午餐時間，台北東區新開了兩家麵館，並排在同一條小巷子裡。左邊的「老薛麵館」開業已有三週，招牌掛得歪歪斜斜；右邊的「阿順麵館」剛剛開張，店面整齊，門口還擺了盆蘭花。兩家麵館的食材來自同一個市場攤位，價格也差不多，老饕之間私下流傳，各自試吃過的人評價幾乎旗鼓相當。理論上，中午飢腸轆轆的上班族應該根據自己的喜好、哪家門口距離近、今天想不想走多兩步來決定要進哪一家。
>
> 然而第一週過去後，情況開始偏斜。周一正午，第一位走進巷子的客人——一個穿格子衫的工程師——在兩家門口猶豫了幾秒，因為左側老薛的玻璃窗比較乾淨，順手推門進去了。第二位客人看見老薛裡面已經有人，覺得「有人坐代表不差」，也跟進去了。到了第三位，她其實對右邊阿順的蘭花更有好感，但老薛門口已經零星排了幾個人，她心想：前面這幾個人應該知道點什麼吧，便安靜地排在隊尾。第四位、第五位客人到達時，看見的是一家有人龍、一家空蕩蕩，決定就更容易了——根本不需要思考，直接跟著排。
>
> 一個月後，老薛每天午餐座無虛席，等位要四十分鐘；阿順隔壁幾乎只有偶爾走錯的外送員進門取餐。幾個同事私下比較過：其實吃過阿順的人，十個裡有六個覺得湯頭更鮮。老薛的老闆娘也察覺有趣，她問過幾個客人為何選擇她家，十個裡有八個給的答案都是「看這邊人多」。阿順的老闆阿順想不透，他的麵並沒有變差，他的衛生也沒有問題，但每天站在門口，看見人潮一批一批走進隔壁，卻幾乎沒有人往他這邊多瞄一眼。
>
> ---
>
> 這個故事呈現的是**資訊瀑布**（informational cascade）。當人們依序做決定、能觀察前人的選擇但看不見前人手中真正掌握的私有資訊時，從某個節點開始，後來者放棄自己的判斷、完全跟隨前人的行動，反而是理性的——因為「前面那麼多人的集體選擇」在統計上看起來比自己手中的一份感受更可靠。在故事裡，格子衫工程師的選擇是個人的；第二位跟進帶有一點推斷；第三位已經開始壓過自己的偏好；到了第四位之後，個人的訊號幾乎全被人龍淹沒。一旦瀑布形成，就算大多數人私下覺得阿順更好，這份真實偏好也永遠不會浮出水面——因為沒有人有足夠的理由「第一個偏離」。

---

## 兩支 skill

| Skill | 領域 | 結構 | 適合什麼時候 |
|---|---|---|---|
| **fable-econ** | 經濟學 | 嚴格 3 段故事 + 1 段揭曉 | 想專注經濟學、要忠實還原 Amanda Askell 2025.3 原版 prompt |
| **fable-explore** | 任何領域 | 自由長度寓言 + 解釋段 | 跨領域學習，可指定難度（國小～專家）、風格（莊子／科幻／伊索／偵探）、批次（`-3`）、互動（`-i`）、選擇題（`-g`）、跨域（`-x`） |

兩支共用的設計：

- 故事中**完全不出現學術術語**——讀者在情節裡「活過」概念，揭曉段才命名
- 揭曉段點原理正式名稱（中英並列）+ 機制 + 故事對映
- **可理解性測試**：寫完問「沒讀過這領域的朋友能不能讀懂？」不能就重寫或換概念
- **數字邏輯一致性**：故事中每個距離／價格／時間都會被讀者驗算
- **直白 ≠ 提早揭**：故事呈現「發生了什麼」，揭曉段才負責「為什麼」

---

## Quick start

需要 [Claude Code](https://claude.com/claude-code) CLI。

```bash
# 1. clone
git clone https://github.com/aeopress/writing-skills.TW.git ~/writing-skills.TW

# 2. symlink 想用的 skill 進 ~/.claude/skills/
ln -s ~/writing-skills.TW/fable-econ     ~/.claude/skills/fable-econ
ln -s ~/writing-skills.TW/fable-explore  ~/.claude/skills/fable-explore

# 3. 重啟 Claude Code，輸入觸發詞
```

---

## 觸發詞

兩支 skill 都支援自然語言觸發，不一定要打 slash。

**fable-econ**
```
/fable-econ                ← 預設一篇
/fable-econ 拍賣理論        ← 指定子領域
/fable-econ 行為經濟學
/fable-econ -i             ← 互動模式：先讀再猜
"用寓言教我一個經濟學原理"
"economics fable"
```

**fable-explore**
```
/fable-explore 拓撲學
/fable-explore 用國小程度教我天文學
/fable-explore 最難的賽局理論
/fable-explore -x 熱力學 資訊理論     ← 跨域結構同構
/fable-explore -s 莊子 神經科學        ← 指定風格
"用故事教我量子力學"
"再一個"                              ← 同一對話內沿用上輪設定
```

---

## 範例集錦

`/fable-econ` 跑出來的（v1.4）：

| Eval | 選用原理 | 場景 |
|---|---|---|
| default | 資訊瀑布 Informational Cascade | 台北東區兩家麵館 |
| 拍賣理論 | 聯結原則 Linkage Principle (Milgrom-Weber 1982) | 漁市公開叫價 vs 密封投標 |
| 行為經濟學 | 雙曲貼現 Hyperbolic Discounting | 四年沒去成的東京進修班 |

`/fable-explore` 跑出來的（v1.4）：

| Eval | 選用概念 | 類比 |
|---|---|---|
| 拓撲學 | Brouwer 不動點定理 | 咖啡師攪拌咖啡的「總有一點沒動」 |
| 國小天文學 | 奧伯斯悖論 Olbers' Paradox | 山上看星空 vs 無限竹林 |
| 跨域 熱力學 ↔ 資訊理論 | Gibbs 自由能 ↔ 互資訊 | 老裁縫的可用布料 vs 偵探的證詞 |

下面是國小版奧伯斯悖論的完整輸出（給小孩看的版本）：

> 小花在外婆家住了一個暑假，外婆住在沒有路燈的山上。第一個晚上，小花推開木門，一抬頭就被嚇了一跳——天上的星星多到她覺得天空應該整片亮白才對。她問外婆：「星星那麼多，為什麼天空還是黑的？」
>
> 外婆沒有馬上回答。第二天早上，她帶小花走進竹林。
>
> 「你往前看。」外婆說。
>
> 小花往前看，竹子一排接一排，好幾百根，密密麻麻。
>
> 「如果竹林一直延伸下去，延伸到你看不見盡頭，你會在哪個縫隙都看到更後面的竹子嗎？」
>
> 小花想了一下，點頭：「應該會，縫隙最後都會被擋住。」
>
> 「那如果竹林真的無限大，」外婆繼續說，「你往哪個方向看，最後都會有一根竹子擋住視線。那這整片竹林應該看起來……」
>
> 「綠色的，全部都是綠色的。」小花說。
>
> 外婆笑了笑，沒再說話。她們在竹林裡走了很久，小花一直想著這件事。
>
> （⋯⋯中略，故事推進到隔晚⋯⋯）
>
> 「那夜空為什麼是黑的？」她最後問。
>
> 外婆把紙放下：「因為竹林有邊界。」
>
> ---
>
> **奧伯斯悖論（Olbers' Paradox）｜天文學**
>
> 如果宇宙無限大又充滿無限星，往哪個方向看都應該碰到一顆星，夜空要像太陽表面一樣亮。就像竹林無限延伸，每條縫隙都會被竹子填死一樣。但夜空是黑的，因為宇宙有年齡——遠處星光還沒走到地球，而且宇宙在膨脹，光被拉長變弱了。

---

## 來源致謝

- **fable-econ** 忠實重現 Amanda Askell 2025 年 3 月 9 日的 X 貼文 [@AmandaAskell/1898862564718923837](https://x.com/AmandaAskell/status/1898862564718923837)
- **fable-explore** 延伸自 Amanda Askell 在 [Newcomer Pod 訪談](https://www.youtube.com/watch?v=0GaKJ4Fp2x4)（2026 年 4 月）「The Parable Prompt to Try With Claude」章節描述的擴展版本

兩個版本的核心都是 Amanda 對「parable as teaching tool」的洞察。Anthropic 沒有官方背書本 repo——我（[@yelban](https://github.com/yelban)）只是把她公開的 prompt 結構化成可重複觸發的 skill。

---

## 怎麼蓋出來的（4 輪迭代）

兩支 skill 的最終版本（v1.4）是用 Anthropic 官方的 [skill-creator](https://github.com/anthropics/claude-cookbooks) skill 跑出來的——4 輪 with-skill vs baseline 對照評測 + 質性 review。每一輪暴露的問題：

| 版本 | 主要修正 |
|---|---|
| v1.1 | 嵌入完整範例（Oakridge Orchards / 燈塔守的紅燈籠）、段落結構從抽象詞改為「§1 基線／§2 摩擦／§3 反轉」、加反例清單、加概念真實性檢核 |
| v1.2 | 加可理解性測試、可類比優於最炫、跨域不疊抽象、數字邏輯一致性反例 |
| v1.3 | 加「直白 ≠ 提早揭」——故事不解釋機制，揭曉段才負責 why |
| v1.4 | 加「輸出第一個字元就是故事第一個字」、領域子分類／命名實體也算術語 |

最終 39/39 結構斷言通過 + 4 輪人工 review 確認可讀性、揭曉時機、數字一致性都穩。完整 iteration loop（test prompts、subagent 輸出、grading、benchmark）的設計可參考 [skill-creator skill](https://github.com/anthropics/claude-cookbooks) 的 README。

這套流程本身可重複——你也可以 fork 這個 repo、改寫 SKILL.md、跑自己的 evals。

---

## License

雙授權：

- **程式碼**（含未來新增的 scripts / hooks）採 **MIT License**——見 [`LICENSE`](LICENSE)
- **寫作內容**（SKILL.md、README.md、文件 prose）採 **CC BY 4.0**——見 [`LICENSE-CC-BY-4.0`](LICENSE-CC-BY-4.0)

衍生作品請保留歸屬聲明：`based on writing-skills.TW by aeopress contributors, https://github.com/aeopress/writing-skills.TW`。
