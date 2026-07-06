# AGENTS.md

給在本 repo 工作的 AI coding agent（Claude Code、Codex 等）的維護契約。參考 [blader/humanizer 的 AGENTS.md](https://github.com/blader/humanizer) 精神，針對本 repo 的多 skill 結構調整。

## 這個 repo 是什麼

六支純 Markdown 的 Claude Code skill，打包成單一 plugin（`.claude-plugin/marketplace.json`）。runtime 產物是各目錄的 `SKILL.md`（YAML frontmatter ＋ 規則本文）＋ `references/` 分層檔。沒有 build step。

| 目錄 | 角色 |
|------|------|
| `humanizer/` | 語言路由入口（判中英 → -tw／-en） |
| `humanizer-tw/` | 中文去 AI 味（原創，本 repo 的主體） |
| `humanizer-en/` | 英文去 AI 味（**fork 自 blader/humanizer，內容逐字同步**） |
| `good-writing-tw/` | 中文節奏琢磨 |
| `fable-econ/`、`fable-explore/` | 寓言生成 |

## 版本同步契約

改任何 skill 行為時，以下必須在**同一次變更**內一起更新：

1. 該 skill `SKILL.md` frontmatter 的 `version`（semver：加規則 = minor，修字 = patch）。
2. `README.md` 中對應的宣稱：pattern 數量（humanizer-en 目前 **33 條**）、synced 版號（目前 **v2.8.2**）、能力列表、實測數字。
3. `.claude-plugin/marketplace.json` 的 `metadata.version`（整包 plugin 的版本）。
4. `humanizer/SKILL.md`（路由入口）若提及姊妹 skill 的 pattern 數或能力，一併更新。

## humanizer-en 上游同步流程

`humanizer-en/SKILL.md` 的 pattern 內容與上游 **逐字一致**，只有三處本地差異：frontmatter（`name: humanizer-en`、路由說明、版號）、H1 後的 fork banner、`compatibility`。同步步驟：

1. 取上游新版 body（第二個 `---` 之後），整段替換，**不要手抄**（用腳本保證逐字）。
2. 更新 frontmatter `version` 與 banner 裡的 synced 版號。
3. 用 diff 驗證：body 與上游的差異應**只有 banner 兩行**。
4. 更新 README／router 的 pattern 數與版號字樣。

## 上游新 pattern 移植到 humanizer-tw 的紀律

上游（或其他來源）的新 tell 中文化進 humanizer-tw 時，**偵測規則與防誤殺 guard 必須同一次落地**：

- 規則進 `SKILL.md` 類別清單＋`references/anti-patterns.md`（前後對照）＋快速檢查清單。
- 對應的「什麼不算」進 `references/detection-guidance-zh.md`。
- `evals/` 的 `build_gold.py` 同時加正例（remove/replace）**與陷阱例（preserve）**，陷阱例專打新規則的誤殺面。

教訓：2026-06 借鑑「句式結構 > 詞彙替換」時一度引入「維度→面向」誤殺（variance 3/3 穩定），補護欄才壓回。**低誤殺（FP 8%）是本專案的差異化，任何新規則都不得以它為代價**；為什麼不用「品質評分 rubric」逼改寫力度，見 [docs/design-no-scoring-rubric.md](docs/design-no-scoring-rubric.md)。

## 改規則後的驗證

評估 harness 在 `evals/humanizer-tw-2026-06-01-antifp/`（**gitignore 排除、僅本地**）：

- gold 語料一律改 `build_gold.py` 再重跑產出，**勿直接編輯 gold.jsonl**（有 `validate()` 把關）。
- 動了偵測／防誤殺規則 → 至少跑對應類別的 smoke；動核心規則 → 跑 full。指標以 **false_positive_rate** 為北極星，recall 為底線。
- 方法與最近一輪數字見 `docs/humanizer-tw-eval-2026-06.md`。

## Frontmatter 慣例（Claude Code v2.1.x）

- 官方欄位用**連字號**形式：`user-invocable`、`argument-hint`（2026-07 對照官方文件確認；底線舊形式已全數改掉）。
- `description`（含 `when_to_use`）合計上限 1,536 字元；`SKILL.md` 本文控制在 500 行內，細節放 `references/`。
- `version` 是 Claude Code 容忍的擴充欄位，官方 packaging 標準未列——validator 會降級為 note，屬預期。

## 寫作慣例

所有中文內容遵循台灣正體用語（不用「質量／視頻／優化」當中國語境詞——注意「優化」本身是合法台灣詞，見 humanizer-tw 防誤殺清單）；中英之間留半形空格；全形標點。README 的 hero 範例必須是**真實跑出來的輸出**，不得手工美化（repo 歷史曾為此全面清過一次捏造範例）。
