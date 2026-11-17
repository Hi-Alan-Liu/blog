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
updated: 2026-11-17
---

gstack 是 Claude Code 的第三方技能擴充套件，提供從構想到部署的完整開發工作流程。本篇整理了 gstack 的核心技能與多角色審查機制，適合想讓 AI 模擬團隊協作的開發者。

> 本篇為 {% post_link claude-code-commands 'Claude Code 常用指令教學' %} 的進階篇，建議先熟悉 Claude Code 基本操作後再閱讀。

<!-- more -->

---

## 技能總覽

以下依開發階段整理常用技能，完整清單可輸入 `/gstack` 查看。

| 階段 | 指令 | 說明 |
|------|------|------|
| 構想 | `/office-hours` | YC 風格腦力激盪（Startup 模式）或 design thinking 發想（Builder 模式） |
| 審查計畫 | `/plan-ceo-review` | CEO 視角審視範圍與策略方向 |
| | `/plan-eng-review` | 工程主管視角審查架構、資料流與測試 |
| | `/plan-design-review` | 設計師視角評估 UI/UX |
| | `/autoplan` | 一鍵執行 CEO + Eng + Design + DX 所有審查 |
| 設計 | `/design-consultation` | 建立設計系統（字體、顏色、間距） |
| | `/design-shotgun` | 產生多個設計變體並比較 |
| 測試與審查 | `/investigate` | 系統化除錯，找出根本原因 |
| | `/qa` / `/qa-only` | QA 測試並修復 Bug／只產出報告 |
| | `/review` | PR 上線前的多專家程式碼審查 |
| | `/codex` | 請 OpenAI Codex 提供第二意見 |
| | `/design-review` | 視覺 QA，找出設計不一致 |
| | `/health` | 整合 linter、型別檢查、測試，算出 0-10 品質分數 |
| 安全 | `/cso` | 安全長模式：secrets 掃描、供應鏈、OWASP、威脅建模 |
| | `/careful` / `/freeze` / `/guard` | 破壞性指令警告／限制編輯目錄／兩者合併 |
| 部署 | `/ship` | 完整上版流程（測試、版本號、PR） |
| | `/land-and-deploy` | 合併 PR 並驗證生產環境 |
| | `/canary` | 部署後監控 |
| 輔助 | `/browse` | 無頭瀏覽器，用於 QA 與資料擷取 |
| | `/checkpoint` | 儲存／恢復工作狀態 |
| 收尾 | `/document-release` | 上版後自動更新文件 |
| | `/retro` | 每週工程回顧 |

---

## 實戰：開發前的多角色計畫審查

gstack 的核心價值是**多角色審查**：讓 AI 從不同專業角度審視計畫，模擬真實團隊的 review。適合在寫程式之前確保方向正確：

```
構想 → 策略審查 → 架構審查 → 設計審查 → 開始實作
```

- **`/office-hours`**：Startup 模式由 Claude 扮演 YC Partner，用六個問題檢驗需求是否真實；Builder 模式適合 side project，聚焦快速驗證。
- **`/plan-ceo-review`**：挑戰前提假設與範圍，有四種模式 —— 放大機會（SCOPE EXPANSION）、選擇性擴展、鎖定範圍嚴格審查（HOLD SCOPE）、砍到最小可行版本（SCOPE REDUCTION）。
- **`/plan-eng-review`**：審查架構、邊界情況、測試策略與效能，每項給 0-10 分與改善建議。
- **`/plan-design-review`**：審查視覺層級、互動與無障礙，說明「做到什麼才算 10 分」。
- **`/autoplan`**：自動跑完所有審查，只把需要你決定的「品味問題」集中呈現。

審查可以迭代：每輪審查後修改計畫，再跑下一輪。計畫檔底部會自動加上 **GSTACK REVIEW REPORT** 表格，追蹤每個審查的次數與發現。

---

## 重點技能

- **`/review`**：派出多位「專家」審查 diff，涵蓋測試、可維護性、安全、效能、資料庫遷移、API 介面、設計與 Red Team。前一次已跳過的 finding，下次不會重複出現。
- **`/cso`**：Daily 模式快速掃描（信心門檻 8/10），Comprehensive 模式做月度深度審計（門檻 2/10，寧可誤報）。
- **`/codex`**：三種模式 —— Code Review（給 pass/fail）、Challenge（對抗找漏洞）、Consult（來回討論）。
- **`/browse` 資料擷取**：除了點擊測試，也能用 `data` 取得 JSON-LD 等結構化資料、`scrape` 批次下載圖片、`network --capture` 攔截 API 回應。
- **Team Mode**：執行 `./setup --team` 後，每次啟動 Claude Code 會在背景自動同步 gstack 版本，團隊不再需要各自更新。

---

## 我的實踐：用 /loop 打造多角色輪循迭代

> 這是我利用 `/loop` 搭配提示詞設計的**自訂工作流程**，不是 gstack 內建功能。

計畫審查用在開發前；**輪循迭代則用於開發中的持續改進**。四個角色排成迴圈，每半小時輪換一次，每輪只做一件事：

| 角色 | 關注重點 |
|------|----------|
| **Architect** | 目錄結構、元件切分、耦合程度 |
| **Developer** | 邏輯簡潔度、重複程式碼 |
| **Reviewer** | 命名語意、邏輯漏洞、專案慣例 |
| **QA** | 異常處理、邊界情況、使用流程 |

每輪的步驟：

1. 從 `ITERATION_LOG.md` 讀取上一輪的角色，輪到下一個角色
2. 以該角色的視角列出改進項目，只實作優先度最高的一項
3. 確認 Dev Server 沒有錯誤、功能正常
4. 把角色、改動檔案、下輪建議寫入 `ITERATION_LOG.md`

用 `/loop` 自動排程：

```
/loop 30m 執行多角色輪循迭代
```

實際四輪接力改進同一個功能（config 環境變數管理）的例子：

| 輪次 | 角色 | 改進內容 |
|------|------|----------|
| #1 | Architect | 將 config 從硬編碼改為環境變數讀取 |
| #2 | Developer | 為 config 加上 zod schema 驗證 |
| #3 | Reviewer | 改善錯誤訊息，提示缺少哪個環境變數 |
| #4 | QA | 修正缺少 DATABASE_URL 時 server 靜默失敗的問題 |

> **每輪只做一件事，做完、做好、記錄清楚。** 角色分離避免單一視角的盲點，小步改進讓風險可控、容易回溯。

---

## 完整生命週期

```bash
/office-hours          # 1. 驗證想法
                       # 2. 進入 Plan Mode 撰寫計畫
/autoplan              # 3. 多角色審查
/loop 30m 執行多角色輪循迭代   # 4. 實作 + 輪循迭代
/qa                    # 5. 功能、視覺、安全、品質檢查
/design-review
/cso
/health
/ship                  # 6. 建立 PR、合併部署、監控
/land-and-deploy
/canary
/document-release      # 7. 更新文件、每週回顧
/retro
```

---

## 延伸閱讀

- 還不熟 Claude Code 基本操作？👉 {% post_link claude-code-commands 'Claude Code 常用指令教學' %}
- 想了解 Skill 是什麼、怎麼自己寫？👉 {% post_link claude-skills-and-docs 'Claude Skill 與 Claude Docs 觀念整理' %}
