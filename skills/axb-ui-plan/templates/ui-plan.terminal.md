# 終端 / TUI 與框架雛形規劃

## 介面範圍

- 目標系統介面：`{{UI_INTERFACE_NAME}}`
- medium：`terminal`
- 需求來源：`{{REQUIREMENT_SCOPE}}`
- 上游依據：`{{UPSTREAM_ARTIFACTS}}`
- 產出順序：`{{OUTPUT_SEQUENCE}}`

## 終端視覺方向

- 風格來源：`{{STYLE_SOURCE}}`
- 本次風格結論：`{{STYLE_CONCLUSION}}`
- 視覺重點：`{{VISUAL_FOCUS}}`（面板版面、資訊密度、顏色可及性）
- 審美原則：`{{AESTHETIC_PRINCIPLES}}`

## 畫面與流程

### 1. `{{SCREEN_1_NAME}}`

- 對應框架檔案：`ui/screens/{{SCREEN_1_FILE}}`
- 主要目的：`{{SCREEN_1_GOAL}}`
- 進入條件：`{{SCREEN_1_ENTRY_CONDITION}}`
- 主要操作 key：`{{SCREEN_1_PRIMARY_KEYS}}`
- 成功轉移：`{{SCREEN_1_SUCCESS_TRANSITIONS}}`
- 失敗回饋：`{{SCREEN_1_ERROR_FEEDBACK}}`

### 2. `{{SCREEN_2_NAME}}`

- 對應框架檔案：`ui/screens/{{SCREEN_2_FILE}}`
- 主要目的：`{{SCREEN_2_GOAL}}`
- 進入條件：`{{SCREEN_2_ENTRY_CONDITION}}`
- 主要操作 key：`{{SCREEN_2_PRIMARY_KEYS}}`
- 成功轉移：`{{SCREEN_2_SUCCESS_TRANSITIONS}}`
- 失敗回饋：`{{SCREEN_2_ERROR_FEEDBACK}}`

### 3. `{{SCREEN_3_NAME}}`

- 對應框架檔案：`ui/screens/{{SCREEN_3_FILE}}`
- 主要目的：`{{SCREEN_3_GOAL}}`
- 進入條件：`{{SCREEN_3_ENTRY_CONDITION}}`
- 主要操作 key：`{{SCREEN_3_PRIMARY_KEYS}}`
- 成功轉移：`{{SCREEN_3_SUCCESS_TRANSITIONS}}`
- 失敗回饋：`{{SCREEN_3_ERROR_FEEDBACK}}`

<!--
  依實際框架數量增減區塊。
  每個框架都應對回真實產品流程，並先在這份 plan 中定義清楚，後續再落地成對應的 ui/screens/*.txt。
-->

## Keybinding 對照表

| Key | 作用 | 目標框架 | 預期結果 |
| --- | --- | --- | --- |
| `{{KEY_1}}` | `{{KEY_1_ACTION}}` | `{{KEY_1_TARGET_FRAME}}` | `{{KEY_1_OUTCOME}}` |
| `{{KEY_2}}` | `{{KEY_2_ACTION}}` | `{{KEY_2_TARGET_FRAME}}` | `{{KEY_2_OUTCOME}}` |
| `{{KEY_3}}` | `{{KEY_3_ACTION}}` | `{{KEY_3_TARGET_FRAME}}` | `{{KEY_3_OUTCOME}}` |

## 狀態轉移清單

1. `{{STATE_1}}` --`{{TRIGGER_1}}`--> `{{STATE_2}}`（`{{OUTCOME_1}}`）
2. `{{STATE_2}}` --`{{TRIGGER_2}}`--> `{{STATE_3}}`（`{{OUTCOME_2}}`）
3. 任何框架 --`{{ERROR_TRIGGER}}`--> 原位顯示錯誤列（不轉移）

## 互動與假資料原則

- 假資料策略：`{{FAKE_DATA_STRATEGY}}`
- 互動原則：`{{INTERACTION_PRINCIPLES}}`
- 內容原則：`{{CONTENT_PRINCIPLES}}`

## 狀態與資訊揭露

- 使用者可見資訊：`{{VISIBLE_INFORMATION}}`
- 必須隱藏資訊：`{{HIDDEN_INFORMATION}}`
- 主要 UI 狀態：`{{UI_STATES}}`
- 角色或權限差異：`{{ROLE_DIFFERENCES}}`

## 驗證與錯誤回饋

- 輸入驗證：`{{INPUT_VALIDATION}}`
- 狀態衝突處理：`{{STATE_CONFLICT_HANDLING}}`
- 使用者可理解錯誤訊息：`{{ERROR_MESSAGE_STRATEGY}}`

## 雛形輸出規劃

- 入口框架：`ui/screens/entry.txt`
- 預計輸出檔案：`{{PLANNED_FRAME_OUTPUTS}}`
- 框架轉移原則：`{{FRAME_TRANSITION_PRINCIPLES}}`
- Review 目標：`{{REVIEW_GOAL}}`

## 終端實作切分建議

- 面板 / 元件切分：`{{PANE_SPLIT_PLAN}}`
- 共用元件或區塊：`{{SHARED_COMPONENTS}}`
- 對後端契約的依賴：`{{API_DEPENDENCIES}}`
- 驗收重點：`{{ACCEPTANCE_FOCUS}}`
