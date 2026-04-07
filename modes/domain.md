# Domain Mode — 業務邏輯提取模式

## 目的

從程式碼、設定、狀態模型與執行入口中，重建專案的核心業務模組、主要操作、狀態/資料關係與跨模組流程。

這個 mode 的重點不是技術架構概覽，而是回答：

- 這個系統在業務上到底做哪些事？
- 每個業務模組對應哪些 entrypoint / logic unit / domain object？
- 哪些操作會建立、更新、刪除、提交、切換、同步或流轉狀態？
- 哪些流程會觸發背景工作、通知或外部整合？

---

## 啟用方式

在呼叫 Core Analyzer 時指定 `mode: domain`。

---

## 建議輸入

1. **Project Profile** — 用來提供初步模組與術語上下文
2. **Entrypoints** — page、component action、route、controller、job、command、event handler 等，幫助辨識使用者或系統可執行的操作
3. **Logic units** — service、hook、store、use case、reducer、mutation handler 等，幫助定位真正的規則所在
4. **Domain objects / State containers / Models / Schema** — 用來理解資料與狀態結構
5. **Background jobs / Console / Cron / Async workers** — 補足非同步或離線操作
6. **README / Docs** — 只用來輔助命名與校正語意，不取代程式碼證據

---

## 額外分析要求

在 Core Analyzer 的基礎分析完成後，Domain mode 額外要求：

### 1. 業務模組分群

- 不要只按資料夾名稱分群
- 應沿著 `entrypoint -> logic unit -> domain object/state container` 的路徑重建業務模組
- 若模組語意不明，保守命名並標記待確認

### 1.5 技術棧適配

- backend 常見線索：route、controller、service、repository、entity、table、job
- frontend 常見線索：page、component、hook、store、query、mutation、form state
- fullstack 常見線索：UI action -> API call -> server operation -> persistence/update -> UI refresh
- 不論技術棧為何，最終輸出都應回到同一套通用抽象，而不是被框架名詞綁死

### 2. 業務操作提取

對每個模組，嘗試列出主要操作：

- 哪個 actor 或系統入口觸發它
- 會改變哪些 domain object、state container 或 persisted resource
- 會經過哪些 logic unit、驗證或轉換
- 是否會產生副作用（通知、背景工作、外部 API、檔案上傳等）

### 3. 狀態與資料關係

- 找出重要 domain object、state container、entity、table、resource 之間的可觀察關係
- 找出代表狀態機的欄位、enum、UI state、workflow step 或 async lifecycle 痕跡
- 若關係只能部分確認，要保守描述

### 4. 跨模組流程

- 找出一個業務操作是否跨 page / component / route / controller / store / job / console / cronjob
- 特別注意同步入口與非同步處理之間的交界

### 5. 嚴格證據要求

- 任何業務結論都必須附上具體程式證據
- 不能只根據 `src/order/`、`ProjectService`、`OrderPage` 這類命名直接假定完整業務意義

---

## mode_specific 輸出結構

在 analysis-result 的 `mode_specific` 欄位中輸出：

```json
{
  "mode_specific": {
    "domain_summary": "一段話描述目前可重建出的核心業務",
    "business_modules": [
      {
        "name": "模組名稱",
        "description": "模組在業務上的角色",
        "operations": ["主要操作名稱"],
        "evidence": ["關鍵證據"]
      }
    ],
    "operations": [
      {
        "name": "業務操作名稱",
        "module": "所屬模組",
        "entrypoints": ["page/action/route/controller/job/command"],
        "logic_units": ["相關 service/hook/store/use-case"],
        "domain_objects": ["相關 domain object / state / persisted resource"],
        "state_changes": ["會改變的狀態或資料"],
        "side_effects": ["通知/背景工作/外部呼叫"],
        "confidence": "low | medium | high"
      }
    ],
    "data_relationships": [
      {
        "source": "來源 domain object / state / resource",
        "target": "目標 domain object / state / resource",
        "relationship": "可觀察到的關係",
        "confidence": "low | medium | high"
      }
    ],
    "state_transitions": [
      {
        "object_or_process": "狀態所屬物件或流程",
        "states": ["狀態列表"],
        "transition_trigger": "轉換觸發條件",
        "confidence": "low | medium | high"
      }
    ],
    "cross_module_flows": [
      {
        "name": "跨模組流程名稱",
        "steps": ["步驟摘要"],
        "confidence": "low | medium | high"
      }
    ],
    "integration_boundaries": [
      {
        "name": "整合邊界名稱",
        "kind": "api | database | browser-storage | queue | file | websocket | third-party",
        "related_operations": ["相關業務操作"],
        "confidence": "low | medium | high"
      }
    ],
    "domain_completeness": "low | medium | high",
    "gaps": ["目前無法從程式碼確定的業務問題"]
  }
}
```

---

## 提取原則

1. **程式碼優先**：README 或 docs 只能幫助命名，不能取代程式碼證據
2. **操作優先**：先回答「系統做什麼」，再補技術結構
3. **狀態變化優先**：會改變資料、UI、流程或外部系統結果的程式通常是核心業務
4. **關聯優先**：核心 domain object、狀態欄位與互動邊界通常比工具函式更能代表業務
5. **保守命名**：不確定時用中性名稱，並在 gaps 中標記
