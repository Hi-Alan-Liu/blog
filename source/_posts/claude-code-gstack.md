---
title: gstack 多角色開發工作流程
author: Alan Liu
tags:
  - Claude Code
  - AI
  - gstack
  - Workflow
categories:
  - Tool
date: 2026-03-24 13:00:00
updated: 2026-04-08
---

gstack 是 Claude Code 的第三方技能擴充套件，提供從構想到部署的完整開發工作流程。本篇整理了 gstack 的核心技能與多角色審查機制，適合想讓 AI 模擬團隊協作的開發者。

> 本篇為 [Claude Code 常用指令教學](/claude-code-commands) 的進階篇，建議先熟悉 Claude Code 基本操作後再閱讀。

<!-- more -->

輸入 `/gstack` 即可查看所有可用技能。以下依開發階段分類整理。

---

## 技能總覽

### 構想與策略

| 指令 | 說明 |
|------|------|
| `/office-hours` | YC 風格的腦力激盪（Startup 模式）或 design thinking 發想（Builder 模式） |
| `/plan-ceo-review` | CEO 視角審視計畫的範圍與策略方向 |

### 架構與設計

| 指令 | 說明 |
|------|------|
| `/plan-eng-review` | 工程主管視角審查架構、資料流與測試覆蓋 |
| `/plan-design-review` | 設計師視角評估 UI/UX 品質 |
| `/plan-devex-review` | 開發者體驗審查，評估 DX 旅程與競品比較 |
| `/design-consultation` | 建立完整的設計系統（字體、顏色、間距） |
| `/design-shotgun` | 產生多個 AI 設計變體，開比較看板收集回饋 |
| `/design-html` | 從核准的 mockup 產生 production-quality HTML/CSS |
| `/autoplan` | 一鍵執行所有審查（CEO + Eng + Design + DX） |

### 測試與審查

| 指令 | 說明 |
|------|------|
| `/investigate` | 系統化除錯，四階段找出根本原因 |
| `/qa` | 系統化 QA 測試，找出 Bug 並自動修復 |
| `/qa-only` | 僅產出 QA 報告，不修改程式碼 |
| `/review` | PR 上線前的程式碼審查，含 Review Army 多專家派遣 |
| `/codex` | OpenAI Codex 第二意見：Code Review、Challenge 對抗、Consult 問答三種模式 |
| `/design-review` | 視覺 QA，找出設計不一致與間距問題 |
| `/devex-review` | 實際測試開發者體驗：文件導覽、TTHW 計時、錯誤訊息截圖 |
| `/health` | 程式碼品質儀表板，整合 linter、type checker、測試，算出 0-10 加權分數 |

### 部署與監控

| 指令 | 說明 |
|------|------|
| `/ship` | 完整的上版流程（測試、版本號、PR） |
| `/land-and-deploy` | 合併 PR 並驗證生產環境 |
| `/setup-deploy` | 設定部署平台（Fly.io、Vercel、Netlify 等）供 land-and-deploy 使用 |
| `/canary` | 部署後的金絲雀監控 |
| `/benchmark` | 效能基準測試與回歸偵測 |
| `/browse` | 無頭瀏覽器，用於 QA 與網站測試 |

### 安全與防護

| 指令 | 說明 |
|------|------|
| `/cso` | 安全長模式，含 secrets 考古、供應鏈掃描、OWASP 審計與威脅建模 |
| `/careful` | 對破壞性指令加上警告防護 |
| `/freeze` | 限制只能編輯指定目錄 |
| `/guard` | 結合 careful + freeze 的完整安全模式 |

### 開發輔助

| 指令 | 說明 |
|------|------|
| `/checkpoint` | 儲存/恢復工作狀態，跨分支交接時不遺失進度 |
| `/pair-agent` | 與遠端 AI agent 共享瀏覽器（支援 OpenClaw、Codex 等） |
| `/open-gstack-browser` | 啟動帶側邊欄的 AI 控制 Chromium，可即時觀看操作 |
| `/learn` | 管理跨 session 的專案學習紀錄，支援搜尋、清理、匯出 |

### 收尾

| 指令 | 說明 |
|------|------|
| `/document-release` | 上版後自動更新所有文件 |
| `/retro` | 每週工程回顧，分析 commit 與工作模式 |

---

## 實戰：開發前的多角色計畫審查

gstack 的核心價值是**多角色審查** — 讓 AI 從不同專業角度審視你的計畫，模擬真實團隊的 review 流程。以下挑選幾個核心指令深入介紹。

適合在寫任何程式碼之前，確保方向正確：

```
構想 → 策略審查 → 架構審查 → 設計審查 → DX 審查 → 開始實作
```

### /office-hours — 驗證想法

提供兩種模式：

- **Startup 模式**：Claude 扮演 YC Partner，問你六個關鍵問題 — 需求是否真實存在、現有替代方案是什麼、目標用戶有多具體等，幫你過濾掉不值得做的功能
- **Builder 模式**：適合 side project 或 hackathon，用 design thinking 的方式腦力激盪，聚焦在快速驗證與原型設計

### /plan-ceo-review — 策略層面

Claude 化身 CEO/創辦人，挑戰你的前提假設、評估範圍是否足夠大膽、確認優先級是否正確。提供四種審查模式：

| 模式 | 適用時機 |
|------|----------|
| SCOPE EXPANSION | 想放大思考，找更大的機會 |
| SELECTIVE EXPANSION | 維持範圍，挑選值得擴展的部分 |
| HOLD SCOPE | 鎖定範圍，用最嚴格標準審查 |
| SCOPE REDUCTION | 砍到最小可行版本 |

### /plan-eng-review — 執行層面

Claude 化身工程主管，審查架構設計、邊界情況、測試策略、效能瓶頸，每個問題給出 0-10 評分和改善建議。

### /plan-design-review — 體驗層面

Claude 化身設計師，審查視覺層級、互動體驗、設計一致性、無障礙性，並給出「做到什麼才算 10 分」的具體建議。

### /autoplan — 一鍵全審

不想逐一執行？`/autoplan` 自動跑完 CEO → Eng → Design → DX 四個審查，根據六項決策原則自動判斷，只將需要你決定的「品味問題」集中呈現。

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

## 重點技能深入介紹

### /review — Review Army 多專家審查

gstack 的 `/review` 不只是一般的 code review，它會派遣多位「專家」從不同面向審查你的 diff：

| 專家 | 關注重點 |
|------|----------|
| Testing | 測試覆蓋與品質 |
| Maintainability | 可維護性與可讀性 |
| Security | 安全漏洞 |
| Performance | 效能瓶頸 |
| Data Migration | 資料庫遷移安全 |
| API Contract | API 介面變更 |
| Design | 設計一致性 |
| Red Team | 對抗性測試 |

跨次審查會自動去重 — 你在前一次 `/review` 已跳過的 finding，下一次不會再出現（除非相關程式碼有變動）。

### /cso — 安全長雙模式

提供兩種運作模式，適合不同頻率的安全檢查：

- **Daily 模式**：快速掃描，信心門檻 8/10，適合日常開發中的安全把關
- **Comprehensive Monthly 模式**：深度審計，信心門檻 2/10（寧可誤報也不放過），適合月度安全檢視

審計範圍包含：secrets 考古（搜尋硬編碼密鑰）、相依套件供應鏈掃描、CI/CD pipeline 安全、LLM/AI 安全、OWASP Top 10、STRIDE 威脅建模，並支援趨勢追蹤。

### /codex — 三種模式的第二意見

| 模式 | 說明 |
|------|------|
| Code Review | 獨立 diff 審查，給出 pass/fail 結果 |
| Challenge | 對抗模式，嘗試找出你程式碼的漏洞與邊界問題 |
| Consult | 問答模式，有 session 延續性，適合來回討論 |

---

## Team Mode：團隊版本同步

v0.15.13 起，gstack 支援 Team Mode，解決多人協作時版本不一致的問題。

```bash
# 啟用 Team Mode（一次性設定）
./setup --team
```

啟用後的效果：

- 在 Claude Code 的 `SessionStart` hook 註冊自動更新腳本
- 每次啟動 Claude Code 時背景同步（零延遲，不影響工作）
- 更新頻率限制為每小時一次，網路失敗不影響正常使用
- 不再需要將 gstack 複製到每個 repo（vendoring 已棄用）

```bash
# 為團隊 repo 產生引導設定
gstack-team-init optional   # 溫和模式：建議安裝
gstack-team-init required   # 強制模式：未安裝則阻擋工作
```

---

## 瀏覽器資料平台

v0.16.0 為 `/browse` 新增了六個資料擷取命令，將無頭瀏覽器從「點擊按鈕的工具」升級為完整的爬蟲與資料擷取平台：

| 命令 | 說明 |
|------|------|
| `media` | 探索頁面上所有圖片、影片、音訊元素，回傳 URL、尺寸、srcset 等資訊 |
| `data` | 擷取頁面嵌入的結構化資料（JSON-LD、Open Graph、Twitter Cards） |
| `download` | 下載任意 URL 或 `@ref` 元素到本地，支援 blob URL 與 base64 輸出 |
| `scrape` | 批次下載頁面上所有媒體，自動去重並產生 `manifest.json` |
| `archive` | 透過 CDP 將完整頁面儲存為 MHTML |
| `network --capture` | 攔截 API 回應內容，取得結構化 JSON 而非脆弱的 DOM 爬取 |

搭配使用範例：

```bash
# 擷取電商頁面的產品結構化資料
$B goto https://example.com/product
$B data                          # 取得 JSON-LD 產品價格、描述

# 批次下載頁面所有圖片
$B goto https://example.com/gallery
$B scrape --images /tmp/gallery  # 自動下載所有圖片

# 攔截 API 回應
$B network --capture --filter api
$B goto https://app.example.com
$B network --bodies              # 查看攔截到的 JSON 回應
```

---

## 我的實踐：用 /loop 打造多角色輪循迭代

> 以下是基於 gstack 工具組合的**自訂工作流程**，不是 gstack 內建功能，而是利用 `/loop` 搭配提示詞設計的迭代機制。

上面的計畫審查適用於開發前；**輪循迭代則用於開發中的持續改進** — 讓四個不同角色的 AI 定期輪流審查並改進你的專案。

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

## 完整生命週期

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
/health                # 程式碼品質分數

# 6. 上版
/ship                  # 建立 PR
/land-and-deploy       # 合併並部署
/canary                # 監控生產環境

# 7. 收尾
/document-release      # 更新文件
/retro                 # 週末回顧本週成果
```

---

## 延伸閱讀

還不熟悉 Claude Code 的基本操作？請先參考：
👉 [Claude Code 常用指令教學](/claude-code-commands)
