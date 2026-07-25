# MEMORY — writing-skills.TW

專案知識筆記。長期累積，供後續 session 快速進入脈絡。

---

## 2026-07：新 tell 移植、上游 sync v2.8.2、Fable 5 重驗

- **humanizer-tw v1.2.0**：從 blader v2.8.0–2.8.2 中文化三個新 tell（短句連發戲劇腔／金句公式／假坦率開場）＋臆測填空（類別十二）。**移植紀律：偵測規則與防誤殺 guard 必須同次落地**（guard 進 detection-guidance-zh.md；gold 同時加陷阱例）——已寫進 AGENTS.md。
- **humanizer-en synced v2.8.2**（`compatibility: any-agent`）。同步一律腳本取上游 body 逐字替換，diff 驗證只剩 fork banner 差異；README／router 的 pattern 數（33）與版號字樣一起改。
- **Fable 5 重驗**（gold 擴至 134 筆，含 c11_newgen 4 正例＋4 陷阱例；transform=`claude-fable-5`、n1）：recall 95.1%、**FP 6.4%**（Opus 基準 8%±1）、c11 recall 100% 零誤殺。競品表未在 Fable 條件重跑。補記在 docs/humanizer-tw-eval-2026-06.md。score.py 的 model 參數直通 claude CLI，傳完整 id `claude-fable-5` 即可，harness 不用改。
- **frontmatter 連字號化**：官方文件（code.claude.com/docs skills，2026-07 查證）欄位是 `user-invocable`／`argument-hint`（連字號）；六支 skill 已全改，validate_structure.py 白名單新舊並容。description＋when_to_use 合計上限 1,536 字元（實測本 repo 最長 712，安全）。
- **設計決策文件**：docs/design-no-scoring-rubric.md——為何不學 kevintsai／academic 的品質評分 rubric（單向改寫壓力、無保真維度；FP 42%／38% vs 本專案 8%）。未來有人提議加評分表先讀這份。
- marketplace.json metadata.version → 1.4.0；新增 AGENTS.md（版本同步契約）。

## 2026-07（第二輪）：非 humanizer 三支的 Fable 5 適配

- **fable-econ／fable-explore v1.5.0**：失敗模式清單補新世代 slop 生成護欄（揭曉段收金句、短句連發造勢、假深刻結語）。不能靠 humanizer-tw 模式二兜底——skill 沒被觸發就不在 context，生成紀律必須寫在生成 skill 本體。
- **good-writing-tw v1.2.0**：①「拆句 ≠ 連發造勢」cross-guard（v1.2.0 的 humanizer-tw 新增短句連發規則後，兩支串接會互打，界線=因果清晰 vs 堆疊語勢）；② 新增「防過度矯正」節＋檢查清單第 8 項＋輸出前雙向回掃——它的硬數字規則（氣口 15-20 字等）原本零煞車，與 design-no-scoring-rubric.md 分析的同型風險；③ 風格樣本優先於預設數字（保住 humanizer-tw voice calibration 的傳遞）。
- **route_eval.py＋route_set.json**（evals/，本地）：六支分流測試（25 題、accept 容忍集設計、NONE 負例）。sonnet n3 實測：accept 100%／primary 96%／NONE 特異度 100%，全題 3/3 一致——「潤稿→good-writing-tw、去 AI 味→humanizer-tw」分工在 description 層已成立，coding rewrite 零誤觸，**description 不需調**。改任何 description 後重跑此測試（已寫進 AGENTS.md）。
- 觀察項（未動）：fable 兩支與 humanizer-tw 本體 8k+ 字，超過 v2.1.x compaction 5,000 token 重附上限；標竿範例移 references/ 會犧牲生成品質（按需載入不保證被讀），暫不瘦身，長對話品質衰退時再議。
- marketplace.json → 1.5.0。

## 2026-07（第三輪）：no-ai-slop 借鑑（humanizer-tw v1.3.0）

- **借鑑來源**：petergyang/no-ai-slop（英文編輯型 skill，voice-first、pass/fail 自檢——其 eval.md 印證我們不用評分 rubric 的立場）。逐 pattern 對照後真缺口 3 個＋紀律 1 條＋工作流 2 項，全數落地：
  - 類別十三新增：**多數人迷思開場**（「沒有人告訴你」）、**冒號揭曉**（「最妙的是：」）、**自問自答**（「為什麼？因為……」）——中文自媒體高頻。
  - **結尾金句「刪除不改寫」**：模型慣性是把爛金句改成更漂亮的金句；結尾案例規定刪掉、用文中最具體一句收尾。
  - 模式一新增**無樣本聲音保留**（從待改稿本身讀 3–5 個聲音信號；純 LLM 輸出從簡）；標註模式新增**不斷言作者身分**（pattern 是證據，不是 AI 檢測器）。
- guard 同次落地（FAQ 真設問、列表／定義冒號、有論據反主流），gold c11 擴至 15 筆（141 總筆），Fable 5 smoke：recall 1.0、FP 0。benchmark 補 TC13。
- ~~humanizer-en 已知缺口（記錄，不本地修）~~ → 已由分家解決，見第四輪。
- **不借**：em dash 寬鬆規則（與 blader §14 衝突）、先問受眾（我們文體感知自動判）。
- marketplace.json → 1.6.0。

## 2026-07（第四輪）：humanizer-en 分家（v3.0.0 獨立演進）

- **決策**：不再逐字跟隨 blader 上游——我們的評測基礎設施（harness／防誤殺紀律／分流測試）已超過上游，跟隨等於讓最慢方定速。分家前先做**最後一次逐字同步到 v2.9.1**（上游 v2.9.x 帶來：no-fabrication 規則、「保留資訊而非形狀」、voice sample 位階高於 em-dash 規則、Invocation Modes、全文精簡到 412 行）。
- **v3.0.0 本地擴充**：§34 faux-insight setups、§35 colon reveals、§36 self-answered questions（皆源自 no-ai-slop 分析）；§32 補 final-aphorism「刪除不改寫」；Invocation Modes 加 **Annotate mode**（不斷言作者身分）；detection guidance 補三條對應 guard。版號斷開上游（3.x=獨立），banner 改 "independently evolved since v3.0.0"。
- **英文 gold 起步**：`build_gold_en.py` → `gold_en.jsonl`（7 筆：4 正例＋3 陷阱例）。Fable 5 smoke：recall 1.0、FP 0。score.py 語言無關，直接吃英文語料。
- **維護規則改寫**（AGENTS.md）：上游改為定期 diff＋逐條 cherry-pick；-tw／-en 雙向回流，各自帶 guard 與 gold。
- 註：上游 v2.8.3 起把 `version` 移到 `metadata.version`（符合官方 packaging 標準）——我們六支仍用頂層 `version`，validator 容忍；若日後要上官方 marketplace 再統一搬。
- marketplace.json → 1.7.0。

---

## humanizer-tw 評估與 humanizer-zh-academic 借鑑（2026-06）

### 結論

- **6 個中文 humanizer skill 對打**（transform=opus、126 筆 gold、variance n3）：去 AI 味 recall 全在 **91–96%**（同類共通能力）；但誤殺率 FP——**humanizer-tw 8%**，5 個競品（academic／kevintsai／shyuan／hong／blader）全在 **29–42%**。
- **humanizer-tw 的差異化 = 低誤殺**（不把合法台灣用語改掉），不是「更會去 AI 味」。且低 FP 不靠犧牲 recall（它 recall 95% 與競品同級）。
- 一句話：這類 humanizer 都會去 AI 味，但只有 humanizer-tw 不會把正確的台灣用語一起改掉。
- 從 `humanizer-zh-academic` 借鑑 4 點進 humanizer-tw：①噪聲預算（別過度均質化）②對稱列舉篇幅隨分量 ③逐段段末總結句 ④句式>詞彙。④ 一度引入「維度→面向」誤殺（variance 3/3 穩定），已補防誤殺護欄修正。
- 那個專案宣稱「AIGC >50%→11%」的數字不可信（無檢測器/樣本數/字數），但手法方向有理；它的高 FP 源於「硬約束數字上限」傾向過度矯正。
- 完整報告：`docs/humanizer-tw-eval-2026-06.md`。

### 三層評估 harness

位置：`evals/humanizer-tw-2026-06-01-antifp/`（**被 .gitignore 排除、本地工具未版控**）。

- 一鍵：`./run_three_layers.sh [smoke|full] [opus|sonnet]`
- **第 1 層 結構**：`validate_structure.py` — 白名單修正官方 validator 對 `user_invocable`／`argument_hint`／`version` 的誤報（這三個是 Claude Code 實際支援欄位）。
- **第 2 層 觸發**：`trigger_eval.py` + `trigger_set.json` — 自製分類器：把 description 放進 system prompt、讓模型對每個請求判 TRIGGER/SKIP。**不要用 skill-creator 的 `run_eval.py`**——它把 skill 當 `.claude/commands` slash command 偵測，對「貼文字即用」的行為型 skill 恆 0、無鑑別力。query 要包成「待分類樣本」（三引號＋「不要執行」），否則子 claude 會直接執行而非分類。
- **第 3 層 品質**：`score.py`（gold 對打，`--transform-model`／`--judge-model`／`--repeats N` variance）；gold 由 `build_gold.py` 生成（**改腳本再重跑，勿直接編 gold.jsonl**，有 `validate()` 把關）；對比表 `build_compare.py [id...]`。
- 指標：recall（remove+replace 滿足率）、china_replace、**false_positive_rate（preserve 被改＝誤殺，antifp 的核心）**、conservative_regression、punct。

### 環境坑（重要）

- **背景 `run_in_background` 的 zsh shell PATH 可能不全**（連 `/bin` 都沒、`export PATH` 又傳不進 python 子進程），導致 score.py 內呼叫 `claude` 出現 `FileNotFoundError`。解法：`/usr/bin/env PATH=... CLAUDE_BIN=/abs/claude /abs/python3 score.py ...`（score.py 已支援讀 `CLAUDE_BIN`，預設仍 "claude"、向後相容）。
- `build_report.py` 有 skill_id 覆蓋 bug（glob 所有 `results_*.json`，多個同 skill_id 互蓋）；用獨立 id 規避，或改用 `build_compare.py`（讀 variance 格式）。
