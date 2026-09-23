# Rule 1 - 交付 tasks.md 前必須執行全量覆蓋掃描（Pre-Delivery Orphan Coverage Sweep）

- Level: `MUST`
- 在完成 `tasks.md` 撰寫後、正式交付前，必須盤點本輪所有輸入產物與拍板決策，確保沒有任何決策或真實現況成為未被消費的孤立產物（Orphaned Artifacts）。
- 下游 `/axb-implement` 受限於最小上下文原則（`skills/axb-implement/rules/技術參照載入與最小上下文判準.md`），實作 subagent 只讀取 task 的 `Read` 與 phase `Shared Must Read`，不得自行通篇預讀 `research.md` 或其他 truth。因此未被 task `Read` 引用或直接交付的項目，在實作層形同消失。
- 具體掃描與斷言項目包含：
  1. **`truth-delta.md` 非 NOOP 項目**：本輪所有標記為 `ADD`、`MODIFY`、`DELETE` 的 truth rows（包含 API contracts, data models, techstack, interface features/dsl），必須 100% 被分配至對應 phase 的 task 處理（Phase 1 Setup、Phase 2 Foundational、Phase 3 Test Alignment 或 Phase 4 Feature）。
  2. **`research.md` 已拍板 Decisions**：所有規範性、演算法、架構或選型決策，必須至少被一個 task 的 `Read` 所引用（例如 `Read: research.md -> Decision 3`），或由具體 task 直接交付；負向決策（如排除某套件）若構成實作約束，應在相關 task 的 `Boundary` 或 `Read` 明示。
  3. **`specs/truth/techstack.md` 新增或異動章節**：涉及編譯參數、版本號注入（如 `VERSION` ldflags）、測試輔助目標（如 `make verify-no-test-sleep`）、測試執行器指令等，必須被 Setup 或 Foundational 的建置/驗證 task 的 `Read` 所引用並具體落地。
- **NOOP 項目豁免**：`truth-delta.md` 中的 `NOOP` 項目屬於已檢查無須變更之審計記錄，不得為其建立實作任務。
- **空集合豁免**：若本輪無 `research.md`，或該檔無任何已拍板 Decisions，該項視為空集合，掃描直接通過；不得為其虛構決策或無中生有地建立任務。
- 存在任何未涵蓋的非 NOOP truth row 或已拍板 Decision 時，`tasks.md` 判定未通過，不得交付。

## Good Example

- 這個例子是好的，因為在交付前逐一斷言每項決策與 truth 都有 task 承接或引用。

```md
Pre-Delivery Orphan Coverage Sweep 對照表：
- research.md Decision 3 (6-step resolver 演算法) -> 引用於 T018 [BDD-GREEN] 的 Read
- research.md Decision 6 & techstack.md VERSION 宣告 -> 引用於 T001 Setup 的 Read 並綁定編譯參數
- research.md Decision 7 & techstack.md verify target -> 由 T002 Makefile task 實作落地
- truth-delta.md 5 筆非 NOOP rows -> 分別對應 T008–T012 (Phase 3) 與 T016 (Phase 4)
- 孤立產物件數：0。掃描通過，准予交付。
```

## Bad Example

- 這個例子是壞的，因為只檢查格式，漏掉 research.md 拍板的核心演算法與建置參數。

```md
檢查了任務皆為 `- [ ] T###`、Feature phase 有 Test Scope，就直接交付。
結果：
- research.md Decision 3 的解析演算法沒有任何 task 讀取，實作時 subagent 只能自己猜測。
- techstack.md 的 VERSION 與 make verify-no-test-sleep 遺漏，沒有任何 task 建立對應設定。
```

# Rule 2 - 孤立產物必須透過補齊 Task 或補充 Read 參照消除

- Level: `MUST`
- 當 Pre-Delivery 掃描發現孤立決策或規格時：
  - 若該項目屬於待建置之基礎建設、設定、腳本或 make target（如遺漏的建置參數或驗證指令），必須在 Phase 1 (`Setup`) 或 Phase 2 (`Foundational`) 補齊對應 task。
  - 若該項目屬於具體業務邏輯、資料規則或演算法細節，必須將該章節加入對應 Feature phase 的 `Shared Must Read` 或具體 Task 的 `Read`。
  - 不得透過刪除 `research.md` 決策、強行將 non-NOOP 改為 NOOP，或忽略掃描結果來消除孤立警告。

## Good Example

- 這個例子是好的，因為發現遺漏後立即在正確的 Phase 補齊 task 與 Read。

```md
掃描發現：`research.md` Decision 7 採納的 `verify-no-test-sleep` 未被引用。
修復：在 T002 (Foundational Makefile task) 的 Read 加入該 Decision，並在 task 描述中明確要求加入該 make target。
再次掃描：孤立產物件數為 0。
```

## Bad Example

- 這個例子是壞的，因為發現孤立決策後選擇無視或私自刪除決策。

```md
掃描發現 Decision 6 (VERSION 參數) 沒人讀，agent 覺得不重要，未補 task 直接宣告驗證通過並交付。
```

# Rule 3 - 交付 tasks.md 前必須執行 Claim→Witness 覆蓋盤點（Claim→Witness Coverage Sweep）

- Level: `MUST`
- 在完成 `tasks.md` 撰寫後、正式交付前，必須對本輪所有規範性條目與 truth 表面宣稱執行 Claim→Witness 覆蓋盤點，並在 `tasks.md` 輸出 `Claim→Witness 盤點對照表`（Claim→Witness Ledger）。
- **晉升門檻原則（Promotion Gate）**：任何規範性條目（FR / NFR / SC / EC）或 truth 表面散文保證，未經見證者絕不得晉升為 truth。存量 truth 採接觸即觸發（triggered on contact）審計。
- 具體盤點與分類要求：
  1. **提煉單位為「原子效果宣稱（Atomic Effect Claim）」**：
     - 由 `spec.md` 的粗粒度驗證意圖提示與本輪修改之 truth 表面（`specs/truth/**`，包含 `techstack.md`、`dsl.md` 序言、DBML 註解等）進行文字掃描（surface-anchored text walk）。
     - 每一條宣稱必須提煉為單一原子效果（如「回滾失敗時舊歷史檔位元組不變」、「崩潰不留殘留暫存檔」）。
     - 機制細節（如使用暫存檔 rename）除手段即契約（如「不進行外部網路請求」）外，不得偽裝成效果宣稱；並須執行**效果強度校準（Effect-strength calibration）**，不可將進程內 crash-safety 誇大宣稱為斷電耐久性（power-loss durability）。
  2. **可觀察宣稱（Observable）分流**：
     - 能透過外部介面或 Gherkin 驗收流程直接觀察者，必須對應至 `specs/truth/features/**` 的 Feature / DSL，並綁定至 Phase 4 的 `[BDD-GREEN]` 實作任務。
  3. **不可觀察宣稱（Unobservable）分流**：
     - 無法由 Gherkin 外部觀察之內部不變量、耐久性、原子性或故障處理，必須建立專屬 `[WITNESS]` 任務。
     - `[WITNESS]` 任務必須明示 `Dependencies: T###`、`Test Scope`（單元測試或故障注入 seam）與 `Falsifier`（能使該保證破局的鑑別性突變手段）。
  4. **免見證核准與反洗白條款（Anti-laundering）**：
     - 經評估確實不可建立自動化見證者，必須循階梯窮盡檢驗（`effect/fault-injection` → `mechanism-seam` → 均不可行才得評估 `accepted-unwitnessed`）。
     - `accepted-unwitnessed` 必須具備明確理由與「為何無任何輸入可使其失效」之說明，並由**人類決策者**在專案宣告的決策面（如專案 ADR）拍板記錄；AI agent 嚴禁自行宣告。
     - 若宣稱降級（Demote），必須自本輪所有 truth 表面（techstack、dsl prologue、dbml 等）徹底刪除散文，不得留下半生不熟的未見證文字。
- 存在任何未被 `[BDD-GREEN]`、`[WITNESS]` 承接，且未在專案決策面合法記錄為 `accepted-unwitnessed` 的規範性宣稱時，`tasks.md` 判定未通過，嚴禁交付。

## Good Example

- 這個例子是好的，因為在交付前逐一盤點不可觀察宣稱，拆為原子效果並綁定鑑別性見證任務。

```md
Claim→Witness 盤點對照表：
- NFR-001 原型宣稱「回滾操作具原子性與耐久性」提煉為兩項原子效果：
  1. CLM-001 (回滾過程崩潰舊檔不損毀) -> [WITNESS] T015 (Test Scope: internal/store/rollback_test.go, Falsifier: 突變為原地寫入時測試紅燈)
  2. CLM-002 (回滾落盤耐久性) -> [WITNESS] T016 (Test Scope: internal/store/fsync_test.go, Falsifier: 註解 f.Sync() 時測試紅燈)
- CLM-003 (跨機房斷電一致性) -> 經階梯評估自動化測試不可行，人類決策者已於專案 ADR-0053 核可為 accepted-unwitnessed，且 truth 表面已清除散文保證。
- 未見證且未核可之宣稱件數：0。掃描通過，准予交付。
```

## Bad Example

- 這個例子是壞的，因為將規範性 MUST 條目當作背景知識放過，任其在 truth 散文留存卻無任何見證任務。

```md
檢查了 truth-delta 的 table 有對應 task，就宣告完成。
結果：
- spec.md NFR-001 的「回滾操作必須具備原子性：寫入暫存檔 + fsync + atomic rename」沒有任何 [WITNESS] 任務。
- techstack.md 與 dsl.md 序言記載了該原子性保證，但整個測試套件中拔掉 fsync 依然全綠。
```

