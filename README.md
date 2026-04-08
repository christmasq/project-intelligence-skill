# Project Intelligence Skill

一個通用型 AI 專案理解 skill。讓 AI 能結構化地分析任何軟體專案的變更意圖、影響範圍與風險。

**不綁定任何特定產業、商業流程或資料模型。** 專案差異透過 Project Profile 調整，核心分析引擎保持通用。

---

## 快速開始

### 1. 為你的專案建立 Profile

把 `profiles/` 中的 sample 複製一份，根據你的專案修改：

```bash
cp profiles/backend-service.sample.json profiles/my-project.json
```

編輯 `my-project.json`，填入你的專案資訊。最少只需要：

```json
{
  "project_name": "my-project",
  "modules": [
    {
      "name": "core",
      "keywords": ["核心邏輯的關鍵字"],
      "important_paths": ["src/core/"]
    }
  ]
}
```

或者讓 AI 幫你生成：

> 請使用 profile-generator，根據這個 repo 的結構生成一份 Project Profile。

### 2. 分析程式碼變更

將 Core Analyzer prompt 與你選擇的 mode 提供給 AI，搭配你的 Project Profile：

**Review 模式（預設）**— 專案感知型程式碼審查：
> 請使用 project-intelligence 的 review mode，分析這次的 git diff。

**Risk 模式** — 回歸風險與影響範圍分析：
> 請使用 project-intelligence 的 risk mode，分析這次變更的風險。

**Memory 模式** — 萃取可沉澱的知識：
> 請使用 project-intelligence 的 memory mode，從這次分析中提取值得記住的知識。

**Doc 模式** — 生成技術文件草稿：
> 請使用 project-intelligence 的 doc mode，為這個模組生成說明文件。

**Onboarding 模式** — 新人引導指南：
> 請使用 project-intelligence 的 onboarding mode，生成專案入門指南。

**Refactor 模式** — 重構機會分析：
> 請使用 project-intelligence 的 refactor mode，分析這個模組的重構機會。

**Domain 模式** — 業務邏輯重建與資料流分析：
> 請使用 project-intelligence 的 domain mode，分析 order 模組的業務邏輯。

**複合指令** — 不確定用哪個 mode？直接描述需求：
> 請使用 project-intelligence 幫我全面分析這次 PR。

Orchestrator 會自動決定執行哪些 mode 以及順序。

### 3. 取得結構化輸出

所有 mode 都會輸出符合 `schemas/analysis-result.schema.json` 的 JSON，包含：

| 欄位 | 說明 |
|------|------|
| `summary` | 一段式摘要 |
| `intent` | 變更意圖 |
| `confidence` | 整體信心程度 |
| `affected_modules` | 受影響的模組 |
| `behavioral_observations` | 觀察到的行為模式 |
| `risks` | 識別到的風險 |
| `suggested_tests` | 建議的測試方向 |
| `assumptions` | 分析中做出的假設 |
| `open_questions` | 無法回答的問題 |
| `mode_specific` | 各 mode 的專屬輸出 |

---

## 檔案結構

```
project-intelligence-skill/
├── README.md                              ← 你正在讀的文件
├── SKILL.md                              ← Skill 正式進入點（AgentSkill 標準格式）
├── skill.yaml                             ← Legacy metadata
├── prompts/system/
│   ├── core-analyzer.md                   ← 核心分析引擎（所有 mode 共用）
│   ├── profile-generator.md               ← Profile 自動生成器
│   ├── knowledge-merger.md                ← 知識合併引擎
│   └── orchestrator.md                    ← 編排層（多步驟流程控制）
├── schemas/
│   ├── project-profile.schema.json        ← Profile 結構定義
│   ├── analysis-result.schema.json        ← 分析結果結構定義
│   └── knowledge-entry.schema.json        ← 知識條目結構定義
├── modes/
│   ├── memory.md                          ← 知識沉澱模式
│   ├── review.md                          ← 程式碼審查模式
│   ├── risk.md                            ← 風險分析模式
│   ├── doc.md                             ← 文件生成模式
│   ├── onboarding.md                      ← 新人引導模式
│   ├── domain.md                          ← 業務邏輯重建模式
│   └── refactor.md                        ← 重構分析模式
├── knowledge/
│   ├── global/                            ← 全域知識（跨專案通用）
│   └── projects/                          ← 各專案沉澱的知識
├── ci/
│   ├── github-actions.yml                 ← GitHub Actions 整合範例
│   └── README.md                          ← CI 整合指南
└── profiles/
    ├── backend-service.sample.json        ← 後端服務範例
    ├── frontend-app.sample.json           ← 前端應用範例
    └── monorepo.sample.json               ← Monorepo 範例
```

---

## 設計原則

### Domain-Agnostic（去領域化）
核心 prompt 與 schema 不包含任何產業、商業流程或特定資料模型假設。如果你的專案有特定的領域術語或流程，透過 Profile 的 `domain_terms` 和 `risk_patterns` 提供。

### Honest Reasoning（誠實推理）
資訊不足時，分析結果必須包含：
- `assumptions`：做出了哪些假設
- `open_questions`：無法回答的問題
- `confidence`：整體信心程度

AI 不會假裝知道它不知道的事。

### Profile-Driven（Profile 驅動）
同一個分析引擎，透過不同的 Profile 適應不同專案。換專案只需換 Profile，不需改 prompt。

### Structured Output（結構化輸出）
所有分析結果都符合 `analysis-result.schema.json`，可以被程式解析、存檔、比對。

---

## Project Profile 指南

Profile 是分析引擎理解你專案的關鍵。但它不需要一步到位。

### 最小可用 Profile

```json
{
  "project_name": "my-project"
}
```

只有名稱也能跑分析，只是精確度較低。

### 建議逐步補充的欄位

| 優先順序 | 欄位 | 為什麼重要 |
|----------|------|-----------|
| 1 | `modules` | 讓分析能正確映射影響範圍 |
| 2 | `tech_stack` | 讓風險模式更精確 |
| 3 | `risk_patterns` | 讓分析知道你的專案特別在意什麼 |
| 4 | `domain_terms` | 讓分析理解專案特有術語 |
| 5 | `analysis_preferences` | 微調分析行為 |

### 撰寫有效的 risk_patterns

好的 risk_patterns 讓分析引擎知道你的專案「特別在意什麼」。以下是不同技術形態的範例：

**有狀態的後端服務**：
```json
{
  "name": "狀態轉換邏輯",
  "description": "任何影響狀態機或狀態轉換的變更",
  "keywords": ["state", "status", "transition", "machine", "workflow"]
}
```

**有外部整合的系統**：
```json
{
  "name": "外部介面契約",
  "description": "對外 API 回應格式或 webhook payload 的變更可能影響整合方",
  "keywords": ["response", "payload", "webhook", "callback", "schema", "contract"]
}
```

**有非同步處理的系統**：
```json
{
  "name": "非同步行為變更",
  "description": "佇列、排程、事件處理的變更可能產生時序問題",
  "keywords": ["queue", "job", "worker", "event", "async", "schedule", "cron"]
}
```

關鍵原則：risk_patterns 描述的是**你的專案特別需要注意的變更類型**，不是通用的程式碼品質規則。

### 用 Profile Generator 自動生成

提供 README、目錄樹、或依賴檔給 profile-generator，它會生成初始 Profile 並標記哪些部分需要人工確認。

---

## Modes 說明

### review（預設）
**用途**：專案感知型程式碼審查

不只看語法和風格，還會考慮變更在專案脈絡中的意義。輸出包含 review items（含嚴重程度與具體建議）和整體評估。

### risk
**用途**：回歸風險與影響範圍分析

深入分析變更可能帶來的回歸風險，提供具體的驗證計畫。適合在合併重要變更前使用。

### memory
**用途**：知識沉澱

從分析結果中萃取值得長期保留的知識片段（行為規則、架構決策、已知陷阱）。搭配 Knowledge Merger 使用，可持續累積專案知識。

### doc
**用途**：技術文件生成

根據程式碼分析產出對團隊有實際幫助的文件草稿。支援模組說明、架構概覽、變更說明、功能指南等文件類型。產出的文件會標記哪些段落需要人工確認。

### onboarding
**用途**：新人引導

為剛加入專案的工程師生成入門指南，包含專案全貌、建議閱讀順序、專案慣例、常見陷阱和開發環境設定。

### domain
**用途**：業務邏輯重建

從實際程式碼路徑重建業務模組、操作流程、狀態流轉與資料關係。沿 `entrypoint → operation → state/data changes → side effects` 路徑分析，而非僅描述目錄結構。輸出包含 business_modules、operations、data_relationships、state_transitions、cross_module_flows 等。

### refactor
**用途**：重構機會分析

識別程式碼中的結構問題（職責混雜、過度耦合、重複邏輯等），按影響/風險/工作量/價值四個維度排序，提供具體的重構策略和安全評估。

---

## 知識管理

### 知識沉澱流程

```
程式碼分析（memory mode）
    ↓
產出 memory_entries
    ↓
Knowledge Merger 比對既有知識庫
    ↓
去重、合併、標記衝突
    ↓
沉澱到 knowledge/ 目錄
```

### 知識條目類型

| 類型 | 說明 |
|------|------|
| `behavioral_rule` | 程式碼中觀察到的行為規則 |
| `architecture_decision` | 架構決策記錄 |
| `known_pitfall` | 已知的陷阱或容易出錯的地方 |
| `pattern` | 專案中的重複模式 |
| `glossary_term` | 專案術語定義 |

### 知識生命週期

每條知識有 `durability`（有效期）和 `status`（狀態）：

- **新知識**進入時狀態為 `unverified`
- 經人工確認後標記為 `active`
- 被新知識取代後標記為 `superseded`
- 過期或不再適用時標記為 `deprecated`

Knowledge Merger 會自動檢查過期候選並建議處理。

---

## 編排層（Orchestrator）

當你的需求涉及多個分析步驟時，Orchestrator 會自動規劃執行流程。

### 常見流程

| 需求 | 自動規劃的流程 |
|------|--------------|
| 全面理解專案 | profile-generator → onboarding mode |
| 分析 PR | review mode ∥ risk mode → memory mode → knowledge-merger |
| 提取業務邏輯 | profile-generator → domain mode → doc mode |
| 重構評估 | refactor mode → risk mode |
| 知識沉澱 | memory mode → knowledge-merger |
| 完整分析循環 | review ∥ risk → memory → knowledge-merger |

`∥` 表示可平行執行。

### 直接使用

大多數情況下你不需要手動呼叫 Orchestrator。直接描述你的需求，AI 會自動判斷是否需要多步驟執行。

---

## CI 整合

Project Intelligence 可以整合到 CI/CD 流程中，在 PR 建立時自動執行分析。

詳見 `ci/README.md`，包含：
- GitHub Actions 範例（`ci/github-actions.yml`）
- 整合架構說明
- 觸發策略建議
- 成本控制建議

---

## 範例：分析一次 git diff

**輸入**：
```
請使用 project-intelligence 的 review mode 分析以下 diff：

Profile: profiles/backend-service.sample.json

[貼上 git diff 內容]
```

**輸出**（簡化）：
```json
{
  "summary": "在 API 路由中新增了一個端點，用於處理資料匯出請求",
  "intent": "新增功能",
  "confidence": "medium",
  "affected_modules": ["api", "services"],
  "behavioral_observations": [
    {
      "title": "匯出端點未實作分頁",
      "confidence": "high",
      "evidence": "handler 中直接查詢全部資料，無 limit/offset 參數"
    }
  ],
  "risks": [
    {
      "title": "大量資料匯出可能導致記憶體問題",
      "severity": "high",
      "affected_area": "api"
    }
  ],
  "suggested_tests": [
    "測試大量資料情境下的匯出行為",
    "測試無資料時的回應格式"
  ],
  "assumptions": [
    "假設此端點會被直接呼叫（非背景任務）"
  ],
  "open_questions": [
    "匯出的資料量預期有多大？",
    "是否需要非同步處理？"
  ]
}
```

---

## 使用流程

### 第一次導入專案

#### Step 1：安裝 Skill

```bash
# 方式一：放到專案內（推薦，可與團隊共享且版控）
cp -r project-intelligence-skill your-project/.claude/skills/project-intelligence

# 方式二：放到全域 skill 目錄（個人使用）
cp -r project-intelligence-skill ~/.claude/skills/project-intelligence
```

#### Step 2：生成 Project Profile

在目標專案中，對 Claude Code 說：

> 請使用 project-intelligence 幫我生成這個專案的 Profile

AI 會：
1. 載入 `prompts/system/profile-generator.md`
2. 掃描 README、目錄樹、依賴檔、代表性程式碼
3. 輸出 `project-profile.json`（符合 schema）+ 生成報告（標明確認/推斷/待確認的部分）

#### Step 3：將 Profile 存入專案

```
your-project/
├── .claude/
│   ├── skills/
│   │   └── project-intelligence/    ← skill 本體
│   └── project-profile.json         ← 生成的 Profile（必須入 git）
```

### 哪些檔案要放到專案 git 中

| 檔案 | 是否入 git | 原因 |
|------|-----------|------|
| `project-profile.json` | ✅ **必須** | 核心上下文，團隊共享，隨專案演進更新 |
| `knowledge/projects/{name}/` | ✅ **建議** | 團隊累積的分析成果，跨會話可用 |
| `knowledge/global/` | ⚠️ 可選 | 跨團隊共享的全域知識 |
| skill 本體 | ⚠️ 看需求 | 想鎖定版本就放專案 git；否則放全域 `~/.claude/skills/` |

**最小必須入 git 的結構：**

```
your-project/
├── .claude/
│   └── project-profile.json          ← 必須，隨專案演進維護
└── .project-knowledge/                ← 建議，團隊知識累積
    ├── auth-middleware-pattern.json
    ├── order-state-machine.json
    └── ...
```

### Profile 的維護時機

- 新增主要模組時 → 更新 `modules`
- 發現新的業務領域時 → 更新 `business_domains`
- 技術棧變更時（加入 Redis、換 ORM 等）→ 更新 `tech_stack`
- 隨時可以請 AI：「根據最近的變更更新 Project Profile」

---

## 團隊開發情境

### 場景 1：日常 PR Review

開發者提交 PR 後：

> 「用 project-intelligence 分析這次 PR」

Orchestrator 自動規劃：`review mode` ∥ `risk mode` → 並行執行

輸出：結構化 review findings（severity 分級）、影響範圍、風險評估、建議測試

### 場景 2：新人 Onboarding

新人加入時：

> 「用 project-intelligence 幫新人生成入門指南」

流程：若沒有 Profile 會先自動生成 → `onboarding mode`

輸出：學習路徑、慣例說明、常見陷阱、開發環境設定

### 場景 3：業務邏輯梳理

PM 或 Tech Lead 想釐清某個模組的業務邏輯：

> 「用 project-intelligence 提取 order 模組的業務邏輯」

流程：`domain mode` → `doc mode`

輸出：業務操作清單、狀態流轉、跨模組依賴、資料關係

### 場景 4：重構前評估

準備重構某個模組前：

> 「用 project-intelligence 評估重構 auth 模組的風險」

流程：`refactor mode` → `risk mode`

輸出：重構候選點、問題類型、建議策略、影響評估

### 場景 5：知識沉澱

Sprint 結束或重大變更後：

> 「用 project-intelligence 把這次分析的重要發現沉澱下來」

流程：`memory mode` → `knowledge-merger`

沉澱到 knowledge 目錄，後續分析時自動參考

### 團隊協作建議

```
┌──────────────────────────────────────────────┐
│            團隊共享（git 版控）                  │
│                                                │
│  project-profile.json   ← Tech Lead 維護       │
│  knowledge/             ← 團隊共同累積          │
│                                                │
├──────────────────────────────────────────────┤
│            個人使用                              │
│                                                │
│  日常 PR review          ← 各開發者自行觸發     │
│  理解不熟悉的模組         ← 新人或跨組支援時用   │
│                                                │
├──────────────────────────────────────────────┤
│            定期活動                              │
│                                                │
│  Sprint 結束  → memory mode 沉澱知識           │
│  重大重構前   → refactor + risk 評估            │
│  新人到職     → onboarding mode 生成指南        │
│  Profile 過時 → 重新跑 profile-generator 更新   │
└──────────────────────────────────────────────┘
```

**關鍵原則：Profile 是團隊共識文件，不是個人筆記。** 修改 Profile 應該像修改 README 一樣——透過 PR 審查，確保團隊對專案結構的理解一致。

---

## 授權

MIT
