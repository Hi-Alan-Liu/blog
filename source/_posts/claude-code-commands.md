---
title: Claude Code 常用指令教學與應用筆記
author: Alan Liu
tags:
  - Claude Code
  - AI
  - CLI
  - Anthropic
  - gstack
categories:
  - Tool
date: 2026-03-24 12:00:00
---

在終端機裡寫程式時，還要切到瀏覽器問 AI？Claude Code 讓你直接在 CLI 中與 AI 協作，完成撰寫、除錯、重構等軟體工程任務。這篇文章整理了我常用的指令與實際應用方式，方便日後快速查閱。

<!-- more -->

Claude Code 是 Anthropic 推出的官方命令列工具，在專案目錄下輸入 `claude` 即可啟動互動式對話，Claude 會自動偵測所在的 Git 專案與環境資訊。輸入 `/exit` 或按下 `Ctrl + C` 即可離開。

---

## 對話內指令

這些指令在 Claude Code 互動介面中使用，以 `/` 開頭。

### /resume — 恢復先前的對話

```
/resume
```

- 恢復上一次中斷的對話，Claude 會保留先前的上下文與記憶
- 可以從歷史對話清單中選擇要恢復哪一個

### /clear — 清除對話紀錄

```
/clear
```

- 清除目前的對話歷史，重新開始
- 適合話題切換或上下文過於冗長時使用

### /compact — 壓縮對話上下文

```
/compact
```

- 將對話歷史進行摘要壓縮，釋放上下文空間（context window，即 AI 一次能記住的資訊量）
- 適合長時間工作，避免對話過長導致 AI 遺忘早期內容
- 可搭配提示詞使用，例如 `/compact 保留所有架構決策`

### /model — 切換模型

```
/model
```

- 切換使用的 Claude 模型（如 Opus、Sonnet、Haiku）
- Opus 最強大、Sonnet 平衡、Haiku 最快速，依任務複雜度選擇

### /fast — 快速模式

```
/fast
```

- 啟用快速輸出模式，使用相同模型但輸出速度更快
- 適合需要快速回應的簡單任務

---

## CLI 啟動參數

這些參數在終端機啟動 Claude Code 時使用，適合自動化或延續特定對話。

### claude --resume — 直接恢復指定對話

```bash
claude --resume <session-id>
```

**實際範例：**

```bash
claude --resume da02b0e2-844b-4ed8-8d16-c57a32e3ba92
```

- 離開對話時，終端機會顯示 `Resume this session with: claude --resume da02b0e2-...`
- 複製該指令即可隨時回到該對話，上下文完整保留
- 適合長時間任務中途需要離開再回來的情境

### claude --dangerously-skip-permissions — 跳過權限確認

```bash
claude --dangerously-skip-permissions
```

- 加上此參數後，所有檔案編輯、Shell 指令都會自動執行，不再逐一詢問確認
- **適用場景**：自動化腳本、CI/CD 整合、或完全信任 Claude 操作的情境
- **注意**：如同名稱所示，這是「危險的」— Claude 可以不經確認直接修改檔案和執行指令，請確保了解風險後再使用
- 可與 `--resume` 組合使用：

```bash
claude --dangerously-skip-permissions --resume da02b0e2-844b-4ed8-8d16-c57a32e3ba92
```

---

## Skill 技能系統

Skill 是 Claude Code 的專業化工作流程，以 `/` 加上技能名稱呼叫，能針對特定任務提供完整的操作步驟。

**常見內建 Skill：**

| 指令 | 說明 |
|------|------|
| `/commit` | 分析變更並建立 Git commit |
| `/review` | 對目前的 diff 進行 code review |
| `/help` | 查看使用說明 |

---

## 記憶與設定

### 記憶系統

Claude Code 支援持久化的記憶系統，能在不同對話間保留重要資訊。

```
請記住：這個專案使用 Tailwind CSS v4
```

- Claude 會將資訊儲存到 `~/.claude/projects/<project>/memory/` 目錄
- 記憶類型包含：使用者偏好（user）、工作回饋（feedback）、專案資訊（project）、外部參考（reference）
- 未來的對話會自動載入相關記憶

### CLAUDE.md — 專案指令檔

在專案根目錄建立 `CLAUDE.md`，Claude 每次啟動都會自動讀取，等同於專案層級的持久化指令。

```markdown
# CLAUDE.md
## 開發規範
- 使用 TypeScript strict mode
- 測試框架使用 Vitest
- Commit message 使用英文
```

- 適合放置編碼規範、架構決策、開發注意事項
- 全域設定可放在 `~/.claude/CLAUDE.md`

---

## gstack 技能套件

gstack 是一套第三方技能擴充，提供從構想到部署的完整開發工作流程。輸入 `/gstack` 即可查看所有可用技能。

### 構想與策略

| 指令 | 說明 |
|------|------|
| `/office-hours` | YC 風格的腦力激盪，驗證想法是否值得開發 |
| `/plan-ceo-review` | CEO 視角審視計畫的範圍與策略方向 |

### 架構與設計

| 指令 | 說明 |
|------|------|
| `/plan-eng-review` | 工程主管視角審查架構、資料流與測試覆蓋 |
| `/plan-design-review` | 設計師視角評估 UI/UX 品質 |
| `/design-consultation` | 建立完整的設計系統（字體、顏色、間距） |
| `/autoplan` | 一鍵執行所有審查（CEO + Eng + Design） |

### 測試與審查

| 指令 | 說明 |
|------|------|
| `/investigate` | 系統化除錯，四階段找出根本原因 |
| `/qa` | 系統化 QA 測試，找出 Bug 並自動修復 |
| `/qa-only` | 僅產出 QA 報告，不修改程式碼 |
| `/review` | PR 上線前的程式碼審查 |
| `/codex` | 使用 OpenAI Codex 提供第二意見 |
| `/design-review` | 視覺 QA，找出設計不一致與間距問題 |

### 部署與監控

| 指令 | 說明 |
|------|------|
| `/ship` | 完整的上版流程（測試、版本號、PR） |
| `/land-and-deploy` | 合併 PR 並驗證生產環境 |
| `/canary` | 部署後的金絲雀監控 |
| `/benchmark` | 效能基準測試與回歸偵測 |
| `/browse` | 無頭瀏覽器，用於 QA 與網站測試 |

### 安全與防護

| 指令 | 說明 |
|------|------|
| `/cso` | 安全長模式，執行 OWASP 審計與威脅建模 |
| `/careful` | 對破壞性指令加上警告防護 |
| `/freeze` | 限制只能編輯指定目錄 |
| `/guard` | 結合 careful + freeze 的完整安全模式 |

### 收尾

| 指令 | 說明 |
|------|------|
| `/document-release` | 上版後自動更新所有文件 |
| `/retro` | 每週工程回顧，分析 commit 與工作模式 |

---

## gstack 實戰：多角色審查流程

gstack 的核心價值是**多角色審查** — 讓 AI 從不同專業角度審視你的計畫，模擬真實團隊的 review 流程。

### 開發前：計畫審查

適合在寫任何程式碼之前，確保方向正確。

```
構想 → 策略審查 → 架構審查 → 設計審查 → 開始實作
```

#### /office-hours — 驗證想法

Claude 扮演 YC Partner，問你六個關鍵問題：需求是否真實存在、現有替代方案是什麼、目標用戶有多具體等，幫你過濾掉不值得做的功能。

#### /plan-ceo-review — 策略層面

Claude 化身 CEO/創辦人，挑戰你的前提假設、評估範圍是否足夠大膽、確認優先級是否正確。提供四種審查模式：

| 模式 | 適用時機 |
|------|----------|
| SCOPE EXPANSION | 想放大思考，找更大的機會 |
| SELECTIVE EXPANSION | 維持範圍，挑選值得擴展的部分 |
| HOLD SCOPE | 鎖定範圍，用最嚴格標準審查 |
| SCOPE REDUCTION | 砍到最小可行版本 |

#### /plan-eng-review — 執行層面

Claude 化身工程主管，審查架構設計、邊界情況、測試策略、效能瓶頸，每個問題給出 0-10 評分和改善建議。

#### /plan-design-review — 體驗層面

Claude 化身設計師，審查視覺層級、互動體驗、設計一致性、無障礙性，並給出「做到什麼才算 10 分」的具體建議。

#### /autoplan — 一鍵全審

不想逐一執行？`/autoplan` 自動跑完 CEO → Eng → Design 三個審查，只將需要你決定的「品味問題」集中呈現。

### 審查可以迭代

gstack 的審查不是一次性的，每次審查後可以直接修改計畫再跑下一輪：

```
第一輪：/plan-ceo-review → 發現範圍需要調整 → 修改計畫
第二輪：/plan-eng-review → 發現架構問題 → 修改計畫
第三輪：/plan-design-review → 發現 UX 流程問題 → 修改計畫
第四輪：/autoplan → 全面確認 → 開始實作
```

計畫檔案底部會自動加上 **GSTACK REVIEW REPORT** 表格，追蹤每個審查的執行次數、狀態和發現。

---

## gstack 進階：開發中的多角色輪循迭代

上面的計畫審查適用於開發前；以下的**輪循迭代則用於開發中的持續改進** — 讓四個不同角色的 AI 定期輪流審查並改進你的專案。

### 核心概念

四個角色排成迴圈，每半小時自動切換，每輪只做一件事：

```
Architect → Developer → Reviewer → QA → Architect → ...
```

| 角色 | 職責 | 關注重點 |
|------|------|----------|
| **Architect** | 架構設計、模組邊界、技術選型 | 目錄結構、元件切分、耦合程度 |
| **Developer** | 功能實作、程式碼品質、可讀性 | 邏輯簡潔度、重複程式碼 |
| **Reviewer** | Code Review、命名規範、邏輯正確性 | 命名語意、邏輯漏洞、專案慣例 |
| **QA** | 邊界測試、錯誤處理、使用者情境 | 異常處理、操作流程順暢度 |

### 每輪 6 步驟

| 步驟 | 內容 |
|------|------|
| 1. 確認上次角色 | 從 `ITERATION_LOG.md` 讀取最後一筆記錄，首次從 Architect 開始 |
| 2. 選擇本次角色 | 依序輪換，不得重複上一輪 |
| 3. 找出改進項目 | 以本輪角色視角審視程式碼，列出改進項目並排定優先級 |
| 4. 實作最高優先項 | 選取一項執行實際改動，改動須明確可驗證 |
| 5. 確認 Dev Server | 無 error 級別 console 輸出、無未捕捉 exception、功能正常 |
| 6. 寫入迭代記錄 | Append 至 `ITERATION_LOG.md` |

### 迭代記錄格式

```markdown
## Iteration #1 — 2026-03-24 14:30
- 角色：Architect
- 上次角色：（首次執行）
- 改進項目：將共用元件抽離至 components/ 目錄
- 改動檔案：src/components/Header.tsx, src/pages/index.tsx
- Dev Server 狀態：✅ 無 console errors
- 下次建議：Developer 應關注新元件的 props 型別定義
```

若記錄過於冗長或已經過兩輪完整循環，則精簡合併歷史記錄。

### 搭配 /loop 自動排程

```
/loop 30m 執行多角色輪循迭代
```

### 實際四輪迭代範例

以下展示四個角色如何接力改進同一個功能（config 環境變數管理）：

| 輪次 | 角色 | 改進內容 | 下輪建議 |
|------|------|----------|----------|
| #1 14:00 | Architect | 將 config 從硬編碼改為環境變數讀取 | Developer 補上型別驗證 |
| #2 14:30 | Developer | 為 config 加上 zod schema 驗證 | Reviewer 檢查錯誤訊息是否清楚 |
| #3 15:00 | Reviewer | 改善驗證錯誤訊息，加上缺少哪個環境變數的提示 | QA 測試缺少環境變數時的啟動行為 |
| #4 15:30 | QA | 修正缺少 DATABASE_URL 時 server 靜默失敗的問題 | Architect 評估是否需要 health check endpoint |

### 核心原則

> **每輪只做一件事，做完、做好、記錄清楚。**

1. **角色分離** — 避免同一視角的盲點，強制從不同面向思考
2. **增量改進** — 每次只改一件事，風險可控、容易回溯
3. **可追蹤** — 所有改動都有記錄，知道為什麼改、改了什麼
4. **持續進化** — 專案在背景中不斷被不同角色打磨

---

## gstack 完整生命週期

從構想到上線的完整指令順序：

```bash
# 1. 驗證想法
/office-hours

# 2. 進入 Plan Mode 撰寫計畫

# 3. 多角色審查迭代
/autoplan

# 4. 開始實作 + 輪循迭代
/loop 30m 執行多角色輪循迭代

# 5. 品質保證
/qa                    # 功能測試
/design-review         # 視覺測試
/cso                   # 安全測試

# 6. 上版
/ship                  # 建立 PR
/land-and-deploy       # 合併並部署
/canary                # 監控生產環境

# 7. 收尾
/document-release      # 更新文件
/retro                 # 週末回顧本週成果
```

---

以上是我目前常用的 Claude Code 指令與 gstack 工作流程，會隨著使用經驗持續更新。
