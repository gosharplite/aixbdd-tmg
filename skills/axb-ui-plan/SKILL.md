---
name: axb-ui-plan
description: 由 PM 於 spec 與 acceptance Gherkin 確認後執行，根據 plan package 的 `spec.md`、已確認的 `features/acceptance/**` 與既有 `ui/**`，產出 plan-side `ui/**` 設計與可 review 的雛形，供 PM review 後 handoff 給 RD。依介面種類與互動表面選擇 medium：web／`frontend` 走 HTML mode（`ui/*.html`）；出貨互動式 TUI 的 `cli` 走 terminal mode（`ui/screens/*.txt`，不產出 HTML）；純行式 `cli` 則略過，不產出 ui-plan。`axb-ui-plan` 不是 truth owner，不寫入 `specs/truth/**`。
disable-model-invocation: true
---

# UI Plan

`axb-ui-plan` 只產出本次迭代的 UI plan 與可 review 的雛形。UI artifact 留在 plan package，作為實作參考與評審材料，不是系統 truth。

## Medium selection（依介面種類與互動表面）

`axb-ui-plan` 先依本輪介面種類與互動表面選定 medium，再產出對應 artifact：

- **web／`frontend` → HTML mode**：維持現行為，雛形為 `ui/*.html`（可點擊、可換頁的高保真靜態頁面）。
- **`cli` with a TUI → terminal mode**：當 CLI 呈現**持久、多區塊、有狀態的互動表面**（畫面／面板、keybinding、彈窗、即時狀態，隨輸入重繪）時走 terminal mode；雛形為 `ui/screens/*.txt`（與終端實際渲染一致的框架），**不產出 HTML**。
- **`cli` plain → skipped**：純行式 CLI（指令、flags、stdout／stderr、exit code，沒有持續互動表面）不產出 ui-plan；其契約由 `/axb-dsl-refine` 以 `specs/truth/features/cli/**` 承接。
- 判斷依據是**互動表面**，不是名稱裡是否出現 CLI；若無法判定，先呼叫 `/axb-clarify`，不自行假設。

# SOP

## Phase 1 -- 對齊 spec、acceptance、medium 與 UI 範圍

1. READ 讀取使用者需求、plan package 的 `spec.md`、已確認的 `features/acceptance/**`、既有 `ui/**`，以及與 UI 有關的既有 `specs/truth/contracts/**`、`specs/truth/features/**`。
2. THINK 依本輪介面種類與互動表面選定 medium（HTML mode／terminal mode／skipped）；若選 skipped，回報後停止，不產出 ui-plan。
3. READ 依選定 medium 讀取對應的 template 與 rule，確認 plan-side UI artifact 與雛形的完成樣貌：
   - HTML mode：`templates/ui-plan.md`、`templates/ui-plan.example.md`、`templates/prototype-entry.html`、`templates/prototype-entry.example.html`、`templates/prototype-screen.html`、`templates/prototype-screen.example.html`、`rules/高保真靜態頁面切分與Flow覆蓋判準.md` 與 `rules/靜態網站雛形與實作計畫邊界判準.md`。
   - terminal mode：`templates/ui-plan.terminal.md`、`templates/ui-plan.terminal.example.md`、`templates/prototype-terminal-entry.txt`、`templates/prototype-terminal-entry.example.txt`、`templates/prototype-terminal-screen.txt`、`templates/prototype-terminal-screen.example.txt`、`rules/高保真靜態頁面切分與Flow覆蓋判準.md` 與 `rules/靜態網站雛形與實作計畫邊界判準.md`。
4. DELEGATE 若缺口會改變使用者可見流程、畫面責任、互動入口、錯誤狀態或與 truth 的對齊方式，呼叫 `/axb-clarify`；未收斂前停止。

## Phase 2 -- 產出 UI plan 與雛形

1. THINK 依需求、已確認的 acceptance Gherkin 與既有 truth 收斂畫面範圍、狀態、主要 flow、可見回饋與錯誤處理；並依 medium 補上對應面向：HTML mode 收斂 accessibility 與 responsive 行為；terminal mode 收斂 keybinding（操作對應）與狀態轉移。
2. WRITE 將 UI 規劃寫入 `specs/plans/NNN-<slug>/ui/ui-plan.md`，使用對應 medium 的 section 骨架（HTML mode 用 `視覺方向`；terminal mode 用 `終端視覺方向`、`畫面與流程`、`Keybinding 對照表` 與 `狀態轉移清單`）。
3. WRITE 依 UI plan 產出或更新對應 medium 的雛形；兩種 medium 皆不得寫入 `specs/truth/**`：
   - HTML mode：`specs/plans/NNN-<slug>/ui/*.html` 與必要靜態資源。
   - terminal mode：`specs/plans/NNN-<slug>/ui/screens/entry.txt`（入口／啟動框架）與 `specs/plans/NNN-<slug>/ui/screens/NN-<name>.txt`（後續框架）；**不得**產出 HTML。
4. READ 回頭檢查 UI plan 與雛形是否對齊 spec、acceptance 與相關既有 truth；若不符合，立即修正。

## Phase 3 -- 交付後續 handoff

1. WRITE 向 PM 回報 UI plan、雛形路徑、本次選定的 medium、剩餘風險，作為 PM 確認後 handoff 給 RD 的規劃依據；本 skill 不寫入 `specs/truth/**`。
