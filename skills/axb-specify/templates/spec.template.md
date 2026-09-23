# 功能規格：{{FEATURE_TITLE}}

**功能分支**: `{{FEATURE_BRANCH}}`

**建立日期**: {{CREATED_DATE}}

**狀態**: 草稿

**輸入**: 使用者描述：「{{USER_INPUT}}」

## 使用者情境與測試 *(必填)*

<!--
  重要：使用者故事必須依商業價值與交付優先順序排列。
  每個使用者故事都必須可以獨立測試，也就是只實作單一故事時，
  仍能形成可展示、可驗證、可交付的最小可行增量。

  每個故事底下都應直接收納：
  1. 驗收情境（Acceptance Criteria）
  2. 專屬功能需求（FR）
  3. 專屬非功能需求（NFR，若有）

  Claim→Witness 義務（驗證意圖標註）：
  每個規範性條目（FR / NFR / SC / EC）都必須標註驗證意圖（Verification Intent）：
  - observable → <對應的驗收情境或 Rule>：可透過驗收 Gherkin 外部觀察並驗證。
  - unobservable → <預期見證層級>：無法由外部 Gherkin 觀察，必須在實作層建立單元 pin 或故障注入 seam（如 unit pin、fault-injection seam）。
  - accepted-unwitnessed：經專案決策面核可接受免見證（必須符合反洗白條款，不得自行濫用）。
  嚴禁遺留任何未標註驗證意圖之規範性條目。

  只有真正無法合理歸屬單一使用者故事、或明確跨越多個故事的需求，
  才應放在後面的「全域需求」區段。
-->

### 使用者故事 1 - {{USER_STORY_1_TITLE}} (Priority: P1)

{{USER_STORY_1_NARRATIVE}}

**為何為此優先級**: {{USER_STORY_1_PRIORITY_RATIONALE}}

**獨立驗證方式**: {{USER_STORY_1_TEST_APPROACH}}

**驗收情境**:

1. **Given** {{USER_STORY_1_SCENARIO_1_GIVEN}}，**When** {{USER_STORY_1_SCENARIO_1_WHEN}}，**Then** {{USER_STORY_1_SCENARIO_1_THEN}}
2. **Given** {{USER_STORY_1_SCENARIO_2_GIVEN}}，**When** {{USER_STORY_1_SCENARIO_2_WHEN}}，**Then** {{USER_STORY_1_SCENARIO_2_THEN}}

**功能需求（FR）**:

- **FR-001**: 系統 MUST {{USER_STORY_1_FR_001}} [Verification Intent: observable → 驗收情境 1]
- **FR-002**: 系統 MUST {{USER_STORY_1_FR_002}} [Verification Intent: observable → 驗收情境 1]
- **FR-003**: 使用者 MUST 能夠 {{USER_STORY_1_FR_003}} [Verification Intent: observable → 驗收情境 2]

<!--
  若此故事目前沒有專屬 NFR，可刪除此小節；
  若需求其實跨越多個故事，請改放到「全域需求」。
-->
**非功能需求（NFR）**:

- **NFR-001**: {{USER_STORY_1_NFR_001}} [Verification Intent: unobservable → {{USER_STORY_1_NFR_001_WITNESS_TIER}}]

---

### 使用者故事 2 - {{USER_STORY_2_TITLE}} (Priority: P2)

{{USER_STORY_2_NARRATIVE}}

**為何為此優先級**: {{USER_STORY_2_PRIORITY_RATIONALE}}

**獨立驗證方式**: {{USER_STORY_2_TEST_APPROACH}}

**驗收情境**:

1. **Given** {{USER_STORY_2_SCENARIO_1_GIVEN}}，**When** {{USER_STORY_2_SCENARIO_1_WHEN}}，**Then** {{USER_STORY_2_SCENARIO_1_THEN}}

**功能需求（FR）**:

- **FR-004**: 系統 MUST {{USER_STORY_2_FR_004}} [Verification Intent: observable → 驗收情境 1]
- **FR-005**: 系統 MUST {{USER_STORY_2_FR_005}} [Verification Intent: observable → 驗收情境 1]

**非功能需求（NFR）**:

- **NFR-002**: {{USER_STORY_2_NFR_002}} [Verification Intent: unobservable → {{USER_STORY_2_NFR_002_WITNESS_TIER}}]

---

### 使用者故事 3 - {{USER_STORY_3_TITLE}} (Priority: P3)

{{USER_STORY_3_NARRATIVE}}

**為何為此優先級**: {{USER_STORY_3_PRIORITY_RATIONALE}}

**獨立驗證方式**: {{USER_STORY_3_TEST_APPROACH}}

**驗收情境**:

1. **Given** {{USER_STORY_3_SCENARIO_1_GIVEN}}，**When** {{USER_STORY_3_SCENARIO_1_WHEN}}，**Then** {{USER_STORY_3_SCENARIO_1_THEN}}

**功能需求（FR）**:

- **FR-006**: 系統 MUST {{USER_STORY_3_FR_006}} [Verification Intent: observable → 驗收情境 1]

**非功能需求（NFR）**:

- **NFR-003**: {{USER_STORY_3_NFR_003}} [Verification Intent: unobservable → {{USER_STORY_3_NFR_003_WITNESS_TIER}}]

---

<!--
  如有需要可新增更多使用者故事，並延續相同骨架：
  標題、敘述、優先級原因、獨立驗證方式、驗收情境、故事專屬 FR、故事專屬 NFR。
-->

### 邊界情況

- **EC-001**: 當 {{EDGE_CASE_1_CONDITION}} 發生時，系統 MUST {{EDGE_CASE_1_EXPECTED_BEHAVIOR}} [Verification Intent: observable → {{EDGE_CASE_1_VERIFICATION_TARGET}}]
- **EC-002**: 當 {{EDGE_CASE_2_CONDITION}} 發生時，系統 MUST {{EDGE_CASE_2_EXPECTED_BEHAVIOR}} [Verification Intent: observable → {{EDGE_CASE_2_VERIFICATION_TARGET}}]
- **EC-003**: 當 {{EDGE_CASE_3_CONDITION}} 發生時，系統 MUST {{EDGE_CASE_3_EXPECTED_BEHAVIOR}} [Verification Intent: observable → {{EDGE_CASE_3_VERIFICATION_TARGET}}]

## 需求 *(必填)*

> 各使用者故事的專屬 FR / NFR 應直接列在故事底下；本節只保留無法合理歸屬單一使用者故事，或明確跨越多個使用者故事的全域需求。

### 全域需求

#### 功能需求

- **FR-007**: 系統 MUST {{GLOBAL_FR_007}} [Verification Intent: observable → {{GLOBAL_FR_007_VERIFICATION_TARGET}}]

<!--
  若目前沒有跨故事的全域功能需求，可刪除此小節。
-->

#### 非功能需求

- **NFR-004**: {{GLOBAL_NFR_004}} [Verification Intent: unobservable → {{GLOBAL_NFR_004_WITNESS_TIER}}]

<!--
  若需求仍不明確，請使用以下格式標示：

  - **FR-XXX**: 系統 MUST {{REQUIREMENT_TEXT}} [NEEDS CLARIFICATION: {{CLARIFICATION_GAP}}]
  - **NFR-XXX**: {{NON_FUNCTIONAL_REQUIREMENT_TEXT}} [NEEDS CLARIFICATION: {{CLARIFICATION_GAP}}]
-->

### 關鍵實體 *(若功能涉及資料，必填)*

- **{{ENTITY_1_NAME}}**: {{ENTITY_1_DESCRIPTION}}
- **{{ENTITY_2_NAME}}**: {{ENTITY_2_DESCRIPTION}}

## 成功標準 *(必填)*

<!--
  定義可衡量、技術中立且可驗證的成功標準。
-->

### 可量測成果

- **SC-001**: {{SUCCESS_CRITERION_001}} [Verification Intent: observable → {{SUCCESS_CRITERION_001_VERIFICATION_TARGET}}]
- **SC-002**: {{SUCCESS_CRITERION_002}} [Verification Intent: unobservable → {{SUCCESS_CRITERION_002_WITNESS_TIER}}]
- **SC-003**: {{SUCCESS_CRITERION_003}} [Verification Intent: observable → {{SUCCESS_CRITERION_003_VERIFICATION_TARGET}}]

## 假設

- {{ASSUMPTION_1}}
- {{ASSUMPTION_2}}
- {{ASSUMPTION_3}}
