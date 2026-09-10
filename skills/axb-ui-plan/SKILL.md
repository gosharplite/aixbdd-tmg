---
name: axb-ui-plan
description: 由 PM 於 spec 與 acceptance Gherkin 確認後執行，根據 plan package 的 `spec.md`、已確認的 `features/acceptance/**` 與既有 `ui/**`，產出 plan-side `ui/**` 設計與靜態雛形，供 PM review 後 handoff 給 RD。`axb-ui-plan` 不是 truth owner，不寫入 `specs/truth/**`。
disable-model-invocation: true
---

# UI Plan

`axb-ui-plan` 只產出本次迭代的 UI plan 與靜態雛形。UI artifact 留在 plan package，作為實作參考與評審材料，不是系統 truth。

# SOP

## Phase 1 -- 對齊 spec、acceptance 與 UI 範圍

1. READ 讀取使用者需求、plan package 的 `spec.md`、已確認的 `features/acceptance/**`、既有 `ui/**`，以及與 UI 有關的既有 `specs/truth/contracts/**`、`specs/truth/features/**`。
2. READ 讀取 `templates/ui-plan.md`、`templates/ui-plan.example.md`、`templates/prototype-entry.html`、`templates/prototype-entry.example.html`、`templates/prototype-screen.html`、`templates/prototype-screen.example.html`、`rules/高保真靜態頁面切分與Flow覆蓋判準.md` 與 `rules/靜態網站雛形與實作計畫邊界判準.md`，確認 plan-side UI artifact 與靜態雛形的完成樣貌。
3. DELEGATE 若缺口會改變使用者可見流程、畫面責任、互動入口、錯誤狀態或與 truth 的對齊方式，呼叫 `/axb-clarify`；未收斂前停止。

## Phase 2 -- 產出 UI plan 與靜態雛形

1. THINK 依需求、已確認的 acceptance Gherkin 與既有 truth 收斂畫面範圍、狀態、主要 flow、可見回饋、錯誤處理、accessibility 與 responsive 行為。
2. WRITE 將 UI 規劃寫入 `specs/plans/NNN-<slug>/ui/ui-plan.md`。
3. WRITE 依 UI plan 產出或更新 `specs/plans/NNN-<slug>/ui/*.html` 與必要靜態資源；不得寫入 `specs/truth/**`。
4. READ 回頭檢查 UI plan 與靜態雛形是否對齊 spec、acceptance 與相關既有 truth；若不符合，立即修正。

## Phase 3 -- 交付後續 handoff

1. WRITE 向 PM 回報 UI plan、靜態雛形路徑、剩餘風險，作為 PM 確認後 handoff 給 RD 的前端規劃依據；本 skill 不寫入 `specs/truth/**`。
