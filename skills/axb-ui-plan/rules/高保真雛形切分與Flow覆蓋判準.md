# Rule 1 - 多畫面雛形固定以入口畫面作為入口

- Level: `MUST`
- 本判準同時涵蓋 web 與 terminal 兩種 medium。
- 若 feature 的真實產品流程需要跨畫面，`axb-ui-plan` 必須固定使用入口畫面作為雛形入口：
  - web：`ui/index.html`。
  - terminal：`ui/screens/entry.txt`。
- 入口畫面本身也必須是產品的一部分，而不是 sitemap、說明頁或純連結目錄。
- 若 feature 不需要跨畫面，仍至少要有一個可操作的產品畫面。

## Good Example

- 這個例子是好的，因為入口本身就是產品入口，而不是額外的導覽文件。

```md
# web
ui/index.html           -> 商品列表入口畫面
ui/product-detail.html  -> 商品詳情畫面
ui/checkout.html        -> 購物車與結帳畫面

# terminal
ui/screens/entry.txt         -> 啟動 / 輸入框架
ui/screens/10-suggestion.txt -> 即時建議框架
ui/screens/20-running.txt    -> 執行狀態框架
```

## Bad Example

- 這個例子是壞的，因為入口只是檔案清單，沒有真正承擔產品流程入口。

```md
ui/index.html
- Link: product-detail.html
- Link: checkout.html
- Link: order-success.html
```

# Rule 2 - 雛形必須覆蓋從入口到主要結果的完整使用者流程

- Level: `MUST`
- `axb-ui-plan` 產出的雛形至少要覆蓋本次 feature 的主流程入口、關鍵中間狀態與主要結果（畫面或框架）。
- 不可只做單一好看的畫面，卻缺少流程中的等待、錯誤、結果或回合狀態。
- 若畫面數量需要裁切，應優先保留完整流程，而不是只保留視覺最吸睛的片段。

## Good Example

- 這個例子是好的，因為它讓 reviewer 能從啟動一路走到完成或錯誤結果。

```md
流程覆蓋（terminal）：
1. 啟動 / 輸入
2. 即時建議（關鍵中間狀態）
3. 執行狀態
4. 完成摘要或錯誤列（主要結果）
```

## Bad Example

- 這個例子是壞的，因為它只呈現中間一畫面，無法 review 真正產品流程。

```md
流程覆蓋：
1. 只做一個完成畫面
2. 沒有入口
3. 沒有輸入、建議或執行流程
```

# Rule 3 - 雛形仍必須可操作，並以假資料模擬真實互動

- Level: `SHOULD`
- web：雖然雛形不連真實後端，仍應提供可點擊按鈕、可切頁導覽、表單輸入與以假資料呈現的狀態切換，讓使用者能感受真實產品節奏。
- terminal：終端無法「點擊」，改以 `ui/ui-plan.md` 的 `Keybinding 對照表`（key → 作用 → 目標框架 → 預期結果）與 `狀態轉移清單` 呈現可操作性，並以假資料渲染各狀態框架。
- 兩種 medium 都不應只交付靜態截圖式內容，讓 reviewer 無法操作或感受互動。

## Good Example

- 這個例子是好的，因為它雖沒有真實後端，仍能模擬基本互動並讓 reviewer 逐鍵追蹤。

```md
# terminal 可操作性（寫在 ui/ui-plan.md）
Keybinding 對照表：
| Key   | 作用         | 目標框架 | 預期結果               |
| Enter | 送出輸入     | entry    | 歷史新增一筆、清單重算 |
| Tab   | 接受目前建議 | entry    | 編輯器插入建議文字     |
| Esc   | 中止執行     | entry    | 顯示中止摘要           |
```

## Bad Example

- 這個例子是壞的，因為它只有靜態畫面，沒有任何可遵循的操作路徑。

```text
┌─ 完成 ──────────────────────────────┐
│ 這就是之後大概會長這樣。             │
└─────────────────────────────────────┘
```
