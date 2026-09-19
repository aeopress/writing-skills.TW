# 評測 harness 改動：字串先判，與「只重新評審」模式

> 日期：2026-09-19　｜　狀態：已實作、已用存檔結果回放驗證
> 範圍：`evals/humanizer-tw-2026-06-01-antifp/` 的 `score.py`、`compare_judges.py`
> 相關：[評估報告](humanizer-tw-eval-2026-06.md)、[不內建評分 rubric 的設計決策](design-no-scoring-rubric.md)

`evals/` 被 `.gitignore` 排除、只存在本機，所以程式改動不會進版。這份文件是改動的完整紀錄，核心規則的程式碼全文附在文末，換機器時可以照著重建。

## 改了什麼

1. **字串先判。**字串比對有把握的檢查項直接判定，其餘才交給 LLM judge。一筆語料的檢查項全部由字串決定時，完全不呼叫 judge。
2. **只重新評審。**`score.py --rejudge-from <結果檔>` 沿用既有結果的改寫稿，不重跑 transform，只換一個 judge 重判。
3. **`compare_judges.py` 檢查兩份結果是不是同一批改寫稿。**不是就中止。

## 為什麼改

### 問題一：九成以上的 judge 工作其實是字串比對

gold 的 318 個檢查項裡，`remove` 171、`replace` 82、`preserve` 50、`punct` 13、`structure` 2。每一項都帶著一個目標字串，而 judge 被問的問題多半就是「這個字串還在不在」。

拿已存檔的兩份結果試算：用最粗的字串判定（目標在或不在），和 judge 的判定一致的比例是

| 結果檔 | judge | 字串判定與 judge 一致 |
|---|---|---|
| `results_humanizer-tw.json` | Claude（sonnet） | 245/258，95.0% |
| `results_humanizer-tw__judge-codex.json` | Codex | 236/248，95.2% |

每筆語料都要多付一次 `claude -p` 的啟動時間與額度，換來的多半是字串比對就能得到的答案。評測跑得慢、貴，直接限制了能重複幾次，而本專案的經驗是重複次數太少的結論不可靠。

### 問題二：judge 比較量到的不是 judge 差異

`compare_judges.py` 的說明寫「對同一批 transform 的判定」，但兩份存檔結果的 `rewritten` 有 **72/100 筆不同**。原因是第二次執行重跑了 transform，而 transform 的快取 key 含 skill 全文，skill 一改就全部重新改寫。

所以 `judge_comparison.json` 裡的一致率 96.8%、kappa 0.816，混進了改寫本身的變異。誤殺率 20% 對 11% 的差距也一樣：兩個 judge 看的多半不是同一份稿子。

## 字串先判的規則

原則：**字串只在足以證明 skill 做對時下判定，從不直接判「沒做對」。**沒把握的一律交給模型，所以這套規則只會減少呼叫，不會替 judge 做困難的決定。

| 檢查類型 | 字串直接判 satisfied 的條件 | 交給模型的情況與原因 |
|---|---|---|
| `remove` | 目標字串不在改寫稿裡 | 還在：可能是合法用法，例如「幾乎沒有人抱怨」裡的「沒有人」 |
| `replace` | 目標不在，而且 `to` 清單至少有一個詞出現 | 目標還在：可能包在別的詞裡，例如「演算法」含「算法」。目標不在但 `to` 也沒出現：要判斷換上的詞是否語意等價 |
| `preserve`、`structure` | 目標字串還在 | 不見了：要判斷是誤殺還是合理改寫 |
| `punct` | 半形不在，而且對應的全形有出現 | 半形不見、全形也沒出現：整句可能被換掉了 |

另外兩個一律交給模型的情況：

- 改寫稿是空字串。
- 改寫稿含 `<diagnosis>`。受測模型漏出診斷區塊時，目標字串會出現在診斷文字裡，字串命中不可信。

`punct` 要求全形出現，是回放時抓到的：`c10_opencc_13` 的受測模型沒有改寫，而是回了一句聊天（「哈哈，吹吹，我隨時都在線！」），半形問號因此消失。只看「半形不在」會把它判成已修正。

每個 check 的結果多一個 `by` 欄，值是 `string` 或 `model`；結果檔多一個 `judge_stats`，記錄有多少檢查項由字串決定、多少筆沒有呼叫 judge。

## 驗證

沒有呼叫任何模型。做法是把 `_judge_model` 換成回放函式，回傳存檔結果裡該檢查項的判定，再讓存檔的改寫稿走一遍新流程，比對新舊判定與指標。

| judge | judge 呼叫筆數 | 字串決定的檢查項 | 判定與存檔不同 | 指標變化 |
|---|---|---|---|---|
| Claude | 30/104（少 71%） | 218/258 | 1 | `overall_recall` 0.875 → 0.880 |
| Codex | 25/100（少 75%） | 208/248 | 0 | 無 |

- 關閉字串先判（`--no-string-first`）時，兩份結果的判定與指標和存檔完全相同。
- `build_judge_prompt(only=...)` 只列出待判的檢查項，編號維持原樣。
- 對存檔的那一對結果檔執行 `compare_judges.py`，會以「72/100 筆改寫稿不同」中止，exit code 1。

**唯一不同的判定**是 `c06_closing_6` 的 `remove`「美好的明天」。改寫稿是「讓我們期待下一次相遇，以更大的熱情迎接明天。」目標字串已經不在，字串判 satisfied；Claude judge 判未滿足，理由是套話的意思還在。Codex judge 在同一類檢查項上 126/126 與字串一致。這是已知的取捨：`remove` 檢查的定義是目標字串被刪除或改寫掉，同義改寫後的殘留要靠 gold 加新的檢查項來抓，不靠 judge 對 `remove` 的從嚴解讀。

**驗證沒有涵蓋的部分**：實際呼叫 `claude -p` 或 `codex exec` 的路徑沒有重跑。模型只被問部分檢查項時，回傳的 JSON 仍用原編號，解析邏輯沒有改，但這一點只有回放驗證，沒有實際呼叫驗證。

## 用法

```bash
cd evals/humanizer-tw-2026-06-01-antifp

# 一般評測：字串先判預設開啟
python3 score.py --skill ../../humanizer-tw

# 重現舊行為：所有檢查項都交給 judge
python3 score.py --skill ../../humanizer-tw --no-string-first

# 比較兩個 judge：第二份沿用第一份的改寫稿
python3 score.py --skill ../../humanizer-tw --judge-engine codex \
    --rejudge-from results_humanizer-tw.json
python3 compare_judges.py results_humanizer-tw.json results_humanizer-tw__rejudge-codex.json
```

- `--rejudge-from` 只支援單輪，和 `--repeats` 大於 1 同時使用會中止。輸出檔名是 `results_<id>__rejudge-<engine>.json`。
- 真的要比較兩批不同的改寫稿，`compare_judges.py` 加 `--allow-different-rewrites`，結果檔會記下 `same_rewrites: false`。
- 要量「judge 之間的差異」時，建議兩邊都加 `--no-string-first`。字串決定的檢查項兩邊必然相同，會把一致率墊高。

## 後續事項

- `judge_comparison.json` 與 `report.md` 裡的 judge 一致率、誤殺率對比，是用不同改寫稿算的，要用 `--rejudge-from` 重跑後更新。這次沒有重跑，因為需要實際呼叫 Codex。
- variance 模式（`--repeats` 大於 1）只存各輪指標、不存改寫稿，所以無法事後重新評審。要支援的話，得讓各輪結果也寫進輸出檔。
- 新增 judge 引擎時，接在 `_judge_model` 即可，字串先判與 `--rejudge-from` 會自動套用。

## 附：核心規則的程式碼

```python
def string_verdict(check: dict, rewritten: str):
    """字串比對有把握時回傳 True（satisfied）；沒把握回傳 None，交給 LLM judge。"""
    if not rewritten.strip() or "<diagnosis>" in rewritten:
        return None  # 空輸出或漏出診斷標籤：字串命中不可信
    t = check["type"]
    if t == "remove":
        return True if check["target"] not in rewritten else None
    if t == "replace":
        if check["target"] in rewritten:
            return None
        return True if any(x in rewritten for x in check["to"]) else None
    if t in ("preserve", "structure"):
        return True if check["target"] in rewritten else None
    if t == "punct":
        # 半形消失還不夠：整句被換掉時半形也會不見，要看到全形出現才算轉換
        if check["from"] in rewritten:
            return None
        return True if any(ch in rewritten for ch in check["to"]) else None
    return None


def judge(entry, rewritten, model, engine, string_first=True):
    decided = {}
    if string_first:
        for i, c in enumerate(entry["checks"]):
            if string_verdict(c, rewritten):
                decided[i] = True
    pending = [i for i in range(len(entry["checks"])) if i not in decided]
    if not pending:
        return {"verdicts": decided, "by_string": sorted(decided)}
    out = _judge_model(entry, rewritten, model, engine, only=set(pending) if decided else None)
    out["verdicts"] = {**out.get("verdicts", {}), **decided}
    out["by_string"] = sorted(decided)
    return out
```

`_judge_model` 是原本的 `judge` 函式本體，多一個 `only` 參數傳給 `build_judge_prompt`，只列出待判的檢查項。`compare_judges.py` 在載入兩份結果後比對共同 id 的 `rewritten`，有任何一筆不同就中止。
