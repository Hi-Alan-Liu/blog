---
title: Claude Code 常用指令教學
author: Alan Liu
tags:
  - Claude Code
  - AI
  - CLI
  - Anthropic
categories:
  - Tool
date: 2026-03-24 12:00:00
---

在終端機裡寫程式時，還要切到瀏覽器問 AI？Claude Code 讓你直接在 CLI 中與 AI 協作，完成撰寫、除錯、重構等軟體工程任務。這篇文章整理了我常用的指令與設定方式，方便日後快速查閱。

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
- 適合長時間任務中途需要離開再回來，但不想重新描述背景的情境

### /clear — 清除對話紀錄

```
/clear
```

- 清除目前的對話歷史，重新開始
- 適合話題切換或上下文過於冗長時使用
- 例如：從除錯切換到寫新功能時，清除舊的對話避免混淆

### /compact — 壓縮對話上下文

```
/compact
```

- 將對話歷史進行摘要壓縮，釋放上下文空間（context window，即 AI 一次能記住的資訊量）
- 適合長時間工作，避免對話過長導致 AI 遺忘早期內容
- 可搭配提示詞使用，指定保留重點，例如：

```
/compact 保留所有架構決策與已修改的檔案清單
```

### /model — 切換模型

```
/model
```

- 切換使用的 Claude 模型（如 Opus、Sonnet、Haiku）
- Opus 最強大、Sonnet 平衡、Haiku 最快速，依任務複雜度選擇
- 例如：複雜的架構重構用 Opus，簡單的格式修正用 Haiku 即可

### /fast — 快速模式

```
/fast
```

- 啟用快速輸出模式，使用相同模型但輸出速度更快
- 適合需要快速回應的簡單任務，例如查詢語法、產生範本程式碼

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

除了內建 Skill，也可以安裝第三方技能套件來擴充功能，例如 [gstack 工作流程](/claude-code-gstack)。

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

## 延伸閱讀

想了解如何用 gstack 技能套件打造多角色審查與開發工作流程，請參考：
👉 [gstack 多角色開發工作流程](/claude-code-gstack)
