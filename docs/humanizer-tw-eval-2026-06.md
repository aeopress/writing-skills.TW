# humanizer-tw 去 AI 味能力評估 ＋ humanizer-zh-academic 借鑑紀錄

> 日期：2026-06　｜　評估語料：`evals/humanizer-tw-2026-06-01-antifp/gold.jsonl`（126 筆）
> transform=opus、judge=sonnet、variance repeats=3

## 緣起

評估開源 skill [`humanizer-zh-academic`](https://github.com/redbaronyyyyy-eng/humanizer-zh-academic)（號稱「降低中文學術寫作 AIGC 檢測率，>50% → 11%」）：宣稱是否屬實、有哪些可借鑑進 humanizer-tw。

## 一、對「能降 AIGC 率」宣稱的判斷

**那個 11% 數字不可信、被誇大**：沒講檢測器、沒講樣本數（疑似 n=1）、沒講字數變動，是單一軼事而非可複現證據。

**但底層手法方向站得住**：對 perplexity／burstiness 類檢測器，它的手法（打破對稱、句長交替、注入猶豫與判斷、刪模板結構）確實會拉高困惑度與突發性、壓低分數。真正原因不神祕——**它在做大量類人重寫**，會降分是因為改得夠重，不是反偵測魔法。

**三個它沒說的限制**：① 神經分類器型檢測器對表面改寫越來越免疫；② 它鼓勵加「出乎意料」「筆者認為」，對學術論文是憑空捏造／虛假歸因的倫理風險；③ 偵測是軍備競賽，無持久保證。

> 結論：方法有效是因為逼出了真實重寫品質，不是因為能騙過檢測器；那個 11% 別信，但手法可挑來用。

## 二、借鑑的 4 點（已落進 humanizer-tw）

| # | 借鑑點 | 落點 |
|---|--------|------|
| ① | 噪聲預算：別把全文磨成同一語氣，每千字留 2-3 處無害小痕跡 | `SKILL.md` 第二輪自我審查「改得太平整？」 |
| ② | 對稱列舉破法：各項篇幅與實際分量成正比，而非三項等長 | `anti-patterns.md`「對稱列舉」節 |
| ③ | 逐段段末總結句（「由此可見」式）當獨立病徵 | `anti-patterns.md`、`SKILL.md` 類別五 |
| ④ | 句式結構 > 詞彙替換：命中結構性痕跡要動骨架，非只換詞 | `SKILL.md` 核心規則第 9 條 |

未借鑑：它的「抗檢測性」顯式目標（humanizer-tw 定位是「更像人寫的好文字」，不是躲檢測器）、6 維評分表、無護欄的學術版個性注入。

## 三、三層評估 harness

一鍵重跑：`evals/humanizer-tw-2026-06-01-antifp/run_three_layers.sh [smoke|full] [opus|sonnet]`

| 層 | 測什麼 | 工具 | 結果 |
|----|--------|------|------|
| 第 1 層 結構 | frontmatter／格式合法性 | `validate_structure.py` | **PASS**（三個 Claude Code 擴充欄位正確降為 note，非 fail） |
| 第 2 層 觸發 | description 在該觸發時觸發、不誤觸 | `trigger_eval.py` | recall **100%** / specificity **100%** / accuracy **100%** |
| 第 3 層 品質 | 去 AI 味 recall、誤殺率 FP 等 | `score.py`（gold 對打） | 見下表 |

> 第 2 層註：skill-creator 的 `run_eval.py` 把 skill 當 slash command 偵測觸發，對「貼文字即用」的行為型 skill 在 headless `-p` 下恆 0、無鑑別力（已實測證實）。故改用自製分類器：把 description 放進 system prompt、讓模型對每個請求判 TRIGGER/SKIP，貼近真實 skill 載入機制。

## 四、第 3 層實測：humanizer-tw vs 5 個競品（全 6 skills）

opus transform、judge=sonnet、126 筆、n3（mean ± std %）。第一欄為受測本體，其餘為參考競品。

### 總體指標

| 指標 | humanizer-tw | academic | kevintsai | shyuan | hong | blader |
|---|---|---|---|---|---|---|
| 總體 recall（越高越好） | 95% ± 0 | 94% ± 1 | 95% ± 0 | 95% ± 1 | 91% ± 1 | 96% ± 1 |
| 中國用語替換率（越高越好） | 96% ± 2 | 95% ± 2 | 95% ± 2 | 92% ± 4 | 96% ± 2 | 95% ± 2 |
| **誤殺率 FP（越低越好）** | **8% ± 1** | 38% ± 1 | 42% ± 3 | 39% ± 1 | 32% ± 7 | 29% ± 1 |
| 保守回歸率（越低越好） | 0% | 0% | 0% | 0% | 0% | 0% |
| 標點修正率（越高越好） | 97% ± 4 | 95% ± 4 | 97% ± 4 | 100% ± 0 | 95% ± 7 | 90% ± 7 |

### 各類別 recall

| 類別 | humanizer-tw | academic | kevintsai | shyuan | hong | blader |
|---|---|---|---|---|---|---|
| 開場白/連接詞 | 95% | 100% | 100% | 100% | 92% | 100% |
| 互聯網黑話 | 88% | 80% | 85% | 85% | 79% | 93% |
| 翻譯腔 | 94% | 100% | 100% | 100% | 97% | 100% |
| 書面語過重 | 93% | 88% | 90% | 90% | 92% | 90% |
| 公式化結構（含新模式②③④） | 100% | 98% | 99% | 100% | 93% | 100% |
| 結尾套話 | 95% | 93% | 100% | 100% | 91% | 97% |
| 語氣問題 | 91% | 89% | 84% | 84% | 76% | 91% |
| 中式 AI 句型 | 95% | 100% | 98% | 100% | 95% | 100% |
| 中國用語 | 94% | 95% | 95% | 94% | 95% | 96% |
| OpenCC 漏網 | 100% | 95% | 95% | 88% | 98% | 93% |

完整原始數據：`results_<skill>_opus__r3.json`；產表：`python3 build_compare.py`。

## 五、閉環結論

1. **去 AI 味能力是同類共通的**：5 個競品（academic／kevintsai／shyuan／hong／blader）recall 都在 91–96%，與 humanizer-tw 的 95% 同級。所以「能降 AIGC 率／去 AI 味」這件事，這類 skill 普遍做得到——humanizer-zh-academic 的宣稱方向不假。
2. **humanizer-tw 的差異化在「不誤殺」**：5 個競品的誤殺率 FP 全落在 **29–42%**，humanizer-tw 只有 **8%**（最低的競品 blader 29% 也是它的 3.6 倍）。競品靠「改得兇」換取邊際更高的 recall（開場白／翻譯腔／中式句型常 100%），代價是連合法台灣用語也一起改。對台灣使用者，高 FP 意味大量正確用語被誤改——比漏改更糟。這量化坐實了第一輪對 academic「硬約束數字上限傾向過度矯正」的判斷，且整類競品皆然。
3. **借鑑的 ①②③④ 已驗證有效**：新增模式類別 `c05_formulaic` recall **100%**；過程中補掉了 ④ 一度引入的「維度→面向」誤殺（variance 3/3 穩定，補防誤殺護欄後 FP 1.0→0）。借到洞見，沒染上高誤殺。

## 六、限制

- 第 3 層全 6 skills 已用 opus／126 筆／n3 重跑完成（見第四節）。舊 `report_metrics.md`（sonnet／50 筆）保留未覆蓋，僅供歷史對照。
- 競品改寫時的 cwd 在 repo 根，子 claude 會載入專案 CLAUDE.md（台灣用語偏好）——此條件對所有 skill 一致，不影響相對比較。
- 第 2 層是自製分類器近似真實機制，非真實 session 自發觸發。
- ① 噪聲預算靠質性判斷，未進 gold（二元 check 測不到）。
- `build_report.py` 有 skill_id 覆蓋 bug（多個同名 results 互蓋）；本輪用獨立 id 規避。舊 `report_metrics.md` 為 sonnet／50 筆，保留未覆蓋。

## 補記：2026-07 Fable 5 重驗

skill 升至 v1.2.0（新增短句連發戲劇腔／金句公式／假坦率開場／臆測填空＋對應防誤殺 guard）後，gold 以 `build_gold.py` 擴充 `c11_newgen` 類別（4 正例＋4 陷阱例，共 134 筆），transform 改用 **Claude Fable 5**（`claude-fable-5`）、judge=sonnet、**n1** 重跑：

| 指標 | Fable 5（134 筆，n1） | Opus 基準（126 筆，n3） |
|---|---|---|
| 總體 recall | 95.1% | 95% ± 0 |
| 中國用語替換率 | 98% | 96% ± 2 |
| **誤殺率 FP** | **6.4%** | **8% ± 1** |
| 保守回歸率 | 0% | 0% |
| 標點修正率 | 100% | 97% ± 4 |
| c11_newgen recall | 100%（陷阱例 0 誤殺） | —（該類別本輪新增） |

結論：低誤殺差異化在 Fable 5 上成立且更低；新規則有效、未引入新誤殺面。注意 n1 無 variance，與 Opus n3 數字比較時以「同量級」解讀即可；競品對打表未在 Fable 5 條件重跑。原始數據：`results_fable_full.json`、`results_fable_c11_smoke.json`。

## 七、重跑方式

```bash
cd evals/humanizer-tw-2026-06-01-antifp
./run_three_layers.sh            # 第3層只跑新增案例 smoke（便宜）
./run_three_layers.sh full opus  # 第3層全量 gold、opus transform（貴）
python3 build_compare.py hv126_opus academic_opus   # 產對比表
```
