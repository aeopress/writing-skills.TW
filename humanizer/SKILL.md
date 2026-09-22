---
name: humanizer
version: "1.0.1"
description: |
  去 AI 味的語言路由入口：先判斷「待處理文字」是繁體中文還是英文，再套用 humanizer-tw 或 humanizer-en 的規則改寫。
  使用時機：使用者打 /humanizer、說「去 AI 味」「humanize」但沒指定語言，或文字中英夾雜。已知語言時直接用對應的姊妹 skill。
  觸發詞：/humanizer、humanize、去 AI 味（語言不限）、中英夾雜。
user-invocable: true
argument-hint: "<要去 AI 味的文字或檔案路徑，中英不限>"
---

# Humanizer（語言路由入口）

這支 skill 本身不定義規則，只做一件事：**判斷待處理文字的語言，路由到對應的去 AI 味 skill**。

## 路由規則

1. 先判斷「**要被改寫的那段文字**」主要是哪種語言（不是看使用者下指令用的語言）：
   - **繁體中文／中文（含簡體）** → 套用 **humanizer-tw**：13 類 AI 痕跡、OpenCC 漏網清洗、中國用語在地化、防誤殺台灣詞、標註模式、個人風格校準。
   - **英文** → 套用 **humanizer-en**：37 patterns（Wikipedia 基底 33 條＋獨立擴充 4 條）、voice calibration、detection guidance、annotate mode。
2. **取得完整規則**：路由後，用 Read 讀對應的姊妹 skill 取得全部細則再動手——`${CLAUDE_SKILL_DIR}/../humanizer-tw/SKILL.md` 或 `${CLAUDE_SKILL_DIR}/../humanizer-en/SKILL.md`（同一 plugin 內；`${CLAUDE_SKILL_DIR}` 由 Claude Code 在載入時展開成本 skill 的絕對目錄，不受目前工作目錄影響）。**完全遵循該 skill 的工作流與輸出格式**（例如中文的 `<diagnosis>`／`<rewrite>`／`<changelog>`）；本入口不另加格式。
3. **中英混合**：以「主要語言」為準路由；若兩種語言份量相當，先問一句要以哪邊規則為主，或分段各自套用對應 skill。

## 注意

- 判語言看的是「**要去 AI 味的內容**」，不是使用者的指令語言。使用者用中文叫你改一段英文，仍走 humanizer-en。
- **簡體中文**先當中文 → humanizer-tw（它本來就處理簡轉繁殘留與在地化）。
- 真的判不準（如極短片段、大量專有名詞）就直接問：「這段要用中文還是英文規則處理？」

> 已知語言時，直接用 `/humanizer-tw` 或 `/humanizer-en` 最直接；本入口是給「不想指定／中英混合」的便利路由。
