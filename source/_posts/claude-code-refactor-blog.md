---
title: 用 Claude Code 重構我的部落格：一場真實的協作紀錄
author: Alan Liu
tags:
  - Claude Code
  - AI
  - Hexo
  - Workflow
categories:
  - Tool
date: 2026-06-13 10:00:00
---

寫了幾年部落格，文章累積到四十幾篇後，各種「小亂」也跟著浮現：檔名大小寫不一致、有篇文章長到滑不到底、想加個全站規則卻得一篇篇手改。這次我沒有開編輯器逐檔處理，而是直接在這個 Hexo 專案裡開了 Claude Code，讓它陪我把這些事一次清掉。這篇就是那場重構的真實紀錄 —— 包含我下的指令、它實際改了什麼，以及踩到的坑。

<!-- more -->

> 本篇假設你已熟悉 Claude Code 的基本操作。還不熟的話，建議先看 {% post_link claude-code-commands 'Claude Code 常用指令教學' %}。

---

## 為什麼是 Claude Code，而不是手動？

部落格維護的痛點，多半不是「不會做」，而是「做起來很煩」：

- **批次性**：全站幾十個檔案要套同一個規則，手動改又慢又容易漏。
- **跨檔案一致性**：拆文章時，連結、front-matter、`<!-- more -->` 都要同步調整。
- **記不住自己的慣例**：每次回來寫都要重新想「我上次標籤是怎麼分類的？」

這三件事剛好都是 Claude Code 的強項：它能讀懂整個 repo 的結構、跨檔案一起改、而且可以把你的慣例寫進 `CLAUDE.md` 長期記住。

---

## 第一步：先讓 Claude 認識這個專案

在動任何刀之前，我先在專案根目錄建立 `CLAUDE.md`，把部落格的「潛規則」寫下來。這檔案 Claude Code 每次啟動都會自動讀取，等於專案層級的常駐指令。

```markdown
# CLAUDE.md

## 寫作慣例
- 文章放在 source/_posts/，檔名一律小寫、用連字號（kebab-case）
- front-matter 必含 title / author / tags / categories / date
- categories 目前只有：Tool、DevOps 等既有分類，沿用勿亂開新類
- 正文第一段為導言，接著放 `<!-- more -->` 作為首頁摘要分隔
- 文末以「延伸閱讀」連結相關文章
```

寫下這些之後，後續每個任務 Claude 都會自動遵守，不用每次重講。**這一步看似多餘，卻是整場重構效率的關鍵** —— 規則寫一次，省下後面幾十次的提醒。

---

## 實戰一：全站檔名小寫化

最早期的文章檔名大小寫混亂（`DotNetMiniExcel.md` 之類），對應到 URL 也不一致。我只丟了一句：

```
把 source/_posts/ 底下所有檔名與資料夾統一改成小寫 kebab-case，
注意 Hexo 的 post_asset_folder 是開的，資料夾名稱要跟著一起改。
```

Claude 做的事：

1. 掃出所有不符規則的檔名
2. 用 `git mv` 逐一改名（保留 Git 歷史，而不是刪檔重建）
3. **提醒我** Windows 檔案系統大小寫不敏感，純粹改大小寫的 `git mv` 可能被忽略，需要兩段式改名（先改成暫名再改回）

最後對應到三個 commit：

```
1a41efa chore: rename all files and folders to lowercase
97198cf chore: rename all files and folders to lowercase
b60414f chore: rename all files and folders to lowercase
```

> 💡 **踩坑點**：在 Windows 上，`git mv Foo.md foo.md` 常常無效，因為系統認為兩者是同一個檔。Claude 會走「`Foo.md` → `foo.tmp.md` → `foo.md`」的兩段式改名繞過。這種平台細節是手動最容易翻車的地方。

---

## 實戰二：把一篇巨無霸文章拆成兩篇

我原本有一篇 Claude Code 文章塞了 375 行，從基礎指令一路講到進階 gstack 工作流程，又臭又長，讀者很難找到重點。我的指令：

```
claude-code-commands.md 太長了，請拆成兩篇：
- 一篇保留「基礎指令教學」
- gstack 相關的進階內容獨立成新文章 claude-code-gstack.md
兩篇要互相用「延伸閱讀」連結，新文章 front-matter 比照舊文章格式。
```

Claude 處理的細節：

| 工作項 | 內容 |
|--------|------|
| 內容切分 | 判斷哪些段落屬於「基礎」、哪些屬於「進階」 |
| front-matter | 新文章自動沿用既有 tags / categories 格式，只調整標題與日期 |
| 雙向連結 | 兩篇文末互加「延伸閱讀」內鏈，用 Hexo 的 `/permalink` 格式 |
| 摘要分隔 | 新文章補上 `<!-- more -->`，首頁摘要才不會爆版 |

成果就是這兩個 commit：

```
b4a1143 拆分 Claude Code 文章為兩篇：基礎指令教學與 gstack 工作流程
        source/_posts/claude-code-commands.md | 239 +------------------
        source/_posts/claude-code-gstack.md   | 241 +++++++++++++++++++++
```

一句話的指令，換來一次乾淨的跨檔案拆分，連結還都接好了。

---

## 實戰三：內容更新與排版

文章拆完後，gstack 那篇的工具描述有些已經過時。我請 Claude 對照最新的技能清單修正，它一口氣補了 125 行新內容（新技能表格、進階功能說明），同時保持既有的表格與標題風格一致：

```
a5c705d 更新 gstack 文章：修正描述、補充新技能與進階功能
        source/_posts/claude-code-gstack.md | 134 +++++++++++++-----
```

這裡的價值不在「會打字」，而在 **Claude 讀得懂既有排版風格並延續它** —— 新增的表格欄位、語氣、層級都跟原文一致，不會看出是後來補的。

---

## 實戰四：一個指令，套用全站新規則

最後我想讓未來的維護更省力，於是把「skill routing」規則加進 `CLAUDE.md`，讓 Claude 下次遇到對應需求時自動呼叫正確的技能：

```
0746e99 chore: add gstack skill routing rules to CLAUDE.md
        CLAUDE.md | 20 ++++++++++++++++++++
```

這就是 `CLAUDE.md` 的複利效應：**規則一旦寫進去，是對「未來所有對話」生效，而不只是這一次。**

---

## 我的幾個心法

實際跑完這輪重構，整理幾個讓 AI 協作更順的原則：

1. **先寫 `CLAUDE.md`，再動手**
   把慣例前置，省下後面無數次的重複說明，也避免 Claude 自作主張開新分類、改錯格式。

2. **一個指令做一件事，並用 commit 切開**
   像上面每個案例都對應獨立 commit。改動小、好驗證、出錯能單獨 revert，比一次「幫我整理整個部落格」可控太多。

3. **善用 `git mv` 保留歷史**
   明確要求 Claude「用 git mv 而非刪檔重建」，文章的 Git blame 與歷史才不會斷掉。

4. **改完一定本地預覽**
   AI 改 Markdown 很穩，但 Hexo 的 `<!-- more -->`、permalink、front-matter 任一處出錯都可能讓頁面爆版。每輪結束我都會跑一次：

   ```bash
   hexo clean && hexo server
   ```

   確認首頁摘要、內鏈、標籤頁都正常，再 commit。

5. **平台細節交給它提醒**
   像 Windows 大小寫不敏感這種坑，與其自己記，不如把環境資訊講清楚，讓 Claude 主動幫你避開。

---

## 小結

這次重構沒有什麼神奇魔法，本質上就是把「煩、但規則明確」的維護工作交給 Claude Code：

- 全站檔名小寫化 → 批次改名 + 保留 Git 歷史
- 巨無霸文章拆兩篇 → 跨檔案切分 + 雙向連結
- 過時內容更新 → 延續既有排版風格
- 全站規則 → 寫進 `CLAUDE.md` 一勞永逸

關鍵不在「AI 會不會寫字」，而在於 **把你的慣例與環境講清楚，剩下的重複勞動就能放心交出去**。如果你也有個年久失修的部落格或專案，不妨開個 Claude Code 從整理檔名開始試試。

---

## 延伸閱讀

- 還不熟 Claude Code 基本操作？👉 {% post_link claude-code-commands 'Claude Code 常用指令教學' %}
- 想把 AI 協作升級成多角色團隊工作流？👉 {% post_link claude-code-gstack 'gstack 多角色開發工作流程' %}
