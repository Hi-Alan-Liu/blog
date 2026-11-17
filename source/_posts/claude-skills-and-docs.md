---
title: Claude Skill 與 Claude Docs 觀念整理：一個管「怎麼做」，一個管「做出來的東西」
author: Alan Liu
tags:
  - Claude Code
  - AI
  - Skill
  - Claude Docs
categories:
  - Tool
date: 2026-11-17 16:00:00
---

用 Claude 一陣子後，常會遇到兩種煩惱：「同樣的事每次都要重新交代一遍」，以及「做出來的文件散落在對話裡，很難跟同事一起改」。Claude 剛好各有一個功能對應這兩件事 —— **Skill** 解決前者，**Claude Docs** 解決後者。這篇整理兩者的觀念、差別，附上可以直接照抄的範例，以及去哪裡找別人寫好的 Skill。

<!-- more -->

> 本篇假設你已會基本操作 Claude Code，還不熟的話可以先看 {% post_link claude-code-commands 'Claude Code 常用指令教學' %}。

---

## 一句話分清楚

| | Skill | Claude Docs |
| --- | --- | --- |
| 是什麼 | 一份**可重複使用的工作說明書** | 一份**可以一起編輯的線上文件** |
| 解決什麼 | 「每次都要重新交代做法」 | 「成品散落在對話裡，不好分享與修改」 |
| 本體 | 一個資料夾，裡面有 `SKILL.md` | claude.ai 上的一個連結 |
| 給誰看 | 給 Claude 看（讓它照著做） | 給人看（你、同事、主管） |
| 比喻 | 新人的 SOP 手冊 | 團隊共用的 Google 文件 |

**Skill 管「怎麼做」，Docs 管「做出來的東西放哪裡」。** 兩者可以搭配使用：用 Skill 規定週報的格式與步驟，讓 Claude 把週報寫進一份 Doc，再讓同事在 Doc 上留言修改。

---

## Part 1：Skill 是什麼

### 本質：一個資料夾 + 一份 SKILL.md

Skill 就是一個資料夾，最少只需要一個 `SKILL.md`：

```text
weekly-report/
├── SKILL.md          ← 必要：說明書本體
├── template.md       ← 選用：參考文件，需要時才讀
└── scripts/
    └── collect.py    ← 選用：可執行的腳本
```

`SKILL.md` 分成兩段：上面是 YAML 格式的**基本資料（frontmatter）**，下面是用 Markdown 寫的**操作步驟**。

```markdown
---
name: weekly-report
description: 撰寫團隊週報。當使用者提到「週報」「本週進度」「weekly report」時使用。
---

# 週報撰寫流程

1. 讀取本週的 git log 與已關閉的 issue
2. 依照 template.md 的格式整理成三段：本週完成、進行中、下週計畫
3. 每段最多 5 點，每點一句話
```

### 關鍵觀念：漸進式載入（Progressive Disclosure）

這是 Skill 最重要、也最容易被忽略的設計：

| 層級 | 什麼時候載入 | 佔用多少 |
| --- | --- | --- |
| `name` + `description` | **一直都在**，Claude 隨時知道有哪些 Skill 可用 | 很少，一兩行 |
| `SKILL.md` 本文 | **需要時才載入**：你呼叫它，或 Claude 判斷用得上 | 中等，建議 500 行內 |
| `template.md`、`scripts/` | **本文裡提到、真的需要時**才讀取或執行 | 不用就不佔 |

所以你可以裝幾十個 Skill，平常幾乎不佔 Claude 的注意力。也因為這樣，**`description` 是整個 Skill 最重要的一行** —— Claude 就是靠它判斷「現在該不該用這個 Skill」。寫得太模糊，它永遠不會被觸發。

### 兩種觸發方式

| 方式 | 說明 |
| --- | --- |
| **你主動呼叫** | 在 Claude Code 輸入 `/weekly-report` |
| **Claude 自動判斷** | 你說「幫我寫這週的週報」，Claude 比對 `description` 後自己載入 |

可以用 frontmatter 控制誰能觸發：

| 設定 | 你能呼叫 | Claude 能自動用 | 適合 |
| --- | --- | --- | --- |
| （預設） | ✅ | ✅ | 大部分情況 |
| `disable-model-invocation: true` | ✅ | ❌ | 部署、發布等**有副作用**的動作，只想自己按 |
| `user-invocable: false` | ❌ | ✅ | 純背景知識，例如公司的命名規範 |

### Skill 放在哪裡（Claude Code）

| 範圍 | 路徑 | 用途 |
| --- | --- | --- |
| 個人 | `~/.claude/skills/<名稱>/SKILL.md` | 所有專案都能用 |
| 專案 | `.claude/skills/<名稱>/SKILL.md` | 跟著 repo commit，團隊共用 |
| Plugin | 透過 `/plugin` 安裝 | 呼叫時是 `/plugin名稱:skill名稱` |

> 以前的「自訂 slash command」（`.claude/commands/xxx.md`）已經併入 Skill，舊檔案仍可使用。新寫的建議直接用 Skill，因為它可以附帶參考文件和腳本。

在 **claude.ai** 網頁版上也能用 Skill：到 **Customize → Skills** 開關內建的 Skill，或把 Skill 資料夾壓成 ZIP 上傳（需先在 **Settings → Capabilities** 開啟「Create and edit files」）。

### Skill 跟 CLAUDE.md 差在哪？

兩者都是「寫給 Claude 看的說明」，差別在**什麼時候讀**：

| | CLAUDE.md | Skill |
| --- | --- | --- |
| 載入時機 | 每次對話開始**一定讀** | **需要時**才讀 |
| 適合放 | 每次都要遵守的規則（程式風格、禁止事項） | 特定任務的完整流程（寫週報、發文、部署） |
| 太長的後果 | 每次對話都佔空間 | 幾乎沒影響 |

判斷方式很簡單：**「每次都要記得」放 CLAUDE.md；「做某件事時才需要」做成 Skill。**

---

## 範例一：幫這個部落格寫一個「新增文章」Skill

這是我自己部落格實際會用到的例子。之前寫文章時遇到站內連結 404 的問題（網站放在 `/blog/` 底下，手寫 `/xxx` 連結會少了路徑），這種「每次發文都要注意」的規則，正適合寫成 Skill。

建立 `.claude/skills/new-post/SKILL.md`：

````markdown
---
name: new-post
description: 在此 Hexo 部落格新增一篇文章。當使用者說「寫一篇文章」「新增文章」「發一篇教學」時使用。
argument-hint: [文章主題]
---

# 新增部落格文章

主題：$ARGUMENTS

## 步驟
1. 檔名用英文小寫加連字號，放在 `source/_posts/<slug>.md`
2. 圖片放在同名資料夾 `source/_posts/<slug>/`，文章內直接寫檔名引用
3. front-matter 依照下方格式，date 用今天
4. 開頭一段導言後加 `<!-- more -->`
5. 寫完執行 `npx hexo generate` 確認沒有錯誤

## front-matter 格式
```yaml
title: 文章標題
author: Alan Liu
tags:
  - 標籤
categories:
  - Tool
date: YYYY-MM-DD HH:mm:ss
```

## 規則
- 站內連結一律用 `{% post_link <slug> '顯示文字' %}`，
  不可寫 `[文字](/slug)`，網站 root 是 /blog/，會 404
- 截圖若含個人資訊（姓名、email），必須先遮蔽
- 不要自動 commit，等使用者確認
````

之後只要輸入：

```text
/new-post Windows 印表機共用設定教學
```

Claude 就會照這份規則產出文章，不會再犯同樣的連結錯誤。

---

## 範例二：附參考文件的 Skill（週報）

當說明很長時，把細節拆到其他檔案，`SKILL.md` 只放流程與「目錄」：

```text
.claude/skills/weekly-report/
├── SKILL.md
├── template.md       ← 週報格式範本
└── examples.md       ← 過去寫得好的週報
```

```markdown
---
name: weekly-report
description: 撰寫團隊週報。當使用者提到「週報」「本週進度」「weekly report」時使用。
---

# 週報撰寫流程

1. 執行 `git log --since="1 week ago" --oneline` 取得本週提交
2. 依照 [template.md](template.md) 的格式整理
3. 語氣與長度參考 [examples.md](examples.md)
4. 寫成一份 Claude Doc，標題為「第 N 週週報」，完成後給我連結
```

注意第 4 步 —— 這就是 **Skill 與 Docs 的搭配**：Skill 決定怎麼寫，成品直接放進 Doc 讓團隊看。

> 💡 **不想從零開始寫？** Anthropic 官方有一個 `skill-creator` Skill，對它說「幫我把剛剛的流程做成一個 Skill」，它會問你幾個問題，再產生完整的資料夾。

---

## Part 2：Claude Docs 是什麼

### 本質：活的文件，不是對話裡的一段文字

Claude Docs 是 2026 年 9 月推出的功能，官方的說法是「**和 Claude 以及你的團隊一起撰寫活文件**」。

過去請 Claude 寫文件，成品是對話中的一大段文字，或是一個下載下來的 Word 檔 —— 要修改就得重新產生整份、再自己比對差異。Claude Docs 則是：

- 文件**存在 claude.ai 上的一個連結**，像 Google 文件一樣
- Claude 修改時是**在原文上直接編輯**，不是重寫整份
- 你可以自己在文件裡打字修改，Claude 也看得到你改了什麼
- 同事可以**留言**，也可以在留言中請 Claude 修改

### 基本操作

| 想做的事 | 怎麼做 |
| --- | --- |
| 建立文件 | 在對話中說「幫我寫一份○○**文件**」，Claude 會建立一份 Doc 並給你連結 |
| 請 Claude 修改 | 回到對話說要改什麼，或在文件上**留言並送給 Claude** |
| 分享 | 文件上方的 **Share** → 選擇組織內的人，給予檢視、留言或編輯權限 |
| 匯出 | 點文件名稱 → **Export** → Word、PDF、Markdown，或複製到 Google Docs、Notion |
| 找回舊版本 | 文件名稱選單中的**版本紀錄** |
| 之後再找到它 | 在 claude.ai 的 **Artifacts** 列表中依標題找 |

在 Claude Code 中也能使用 —— 對它說「把這次的調查結果寫成一份文件」，它會建立 Doc 並回傳連結，不會在你的專案資料夾裡多出檔案。

### 什麼時候用 Docs？

| 適合用 Docs | 不需要用 Docs |
| --- | --- |
| 要給別人看、要一起改的：提案、會議紀錄、規格書、週報 | 一次性的問答、快速比較 |
| 會反覆修改很多輪的長文件 | 屬於程式碼的一部分（README、技術文件 → 放 repo） |
| 需要收集意見、留言討論 | 只有自己看一次就丟的筆記 |

---

## 範例三：Docs 的實際對話

**建立：**

```text
幫我寫一份「部落格改版提案」文件，包含：
目前的問題、改版目標、預計時程（表格）、需要的資源。
寫成 Doc 讓我分享給同事。
```

**修改（在對話中）：**

```text
時程表的第二階段延後一週，並在「需要的資源」加上設計人力。
```

**修改（在文件上）：** 選取「預計三個月完成」這段文字 → 留言「這個時程太樂觀，請改成四個月並說明原因」→ 送給 Claude。Claude 會修改那段文字，並在留言串中回覆它改了什麼。

**交付：** 同事看完沒問題後，點文件名稱 → Export → PDF，寄給主管。

---

## Skill + Docs 搭配起來

把兩者串起來，就是一個可以每週重複的流程：

1. **Skill（weekly-report）** 規定資料從哪裡來、格式長怎樣
2. 每週五對 Claude 說「寫這週的週報」，它自動套用 Skill
3. 成品寫進一份 **Doc**，分享給團隊
4. 同事在 Doc 上留言補充，Claude 依留言修正
5. 定稿後匯出 PDF 存檔

**Skill 讓品質穩定，Docs 讓協作順暢。**

---

## 去哪裡找別人寫好的 Skill？

| 來源 | 內容 | 適合 |
| --- | --- | --- |
| [anthropics/skills](https://github.com/anthropics/skills) | **Anthropic 官方** repo，含 Word、Excel、PowerPoint、PDF 文件處理、設計、開發測試等範例，以及 `template/` 範本 | 第一個要看的地方，學寫法最好的範本 |
| Claude Code 的 `/plugin` | 在 Claude Code 中輸入 `/plugin marketplace add anthropics/skills`，再用 `/plugin` 瀏覽、安裝 | 想直接裝來用 |
| claude.ai 的 **Customize → Skills** | 內建 Skill 開關；Team / Enterprise 方案有 Skill 目錄可一鍵安裝 | 不用終端機的同事 |
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 社群整理的大型 Skill 清單，涵蓋 claude.ai、Claude Code、API | 找特定用途的 Skill |
| [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) | 偏向 Claude Code 的社群清單，附安全性說明 | Claude Code 使用者 |
| [agentskills.io](https://agentskills.io) | Agent Skills 開放標準的規格 | 想深入了解格式規範 |
| {% post_link claude-code-gstack 'gstack 多角色開發工作流程' %} | 我之前介紹過的第三方 Skill 套件，一次提供數十個開發流程 Skill | 想看大型 Skill 套件怎麼設計 |

### ⚠️ 安裝第三方 Skill 前

Skill 可以附帶**會被執行的腳本**，也可以設定 `allowed-tools` 讓某些指令免確認執行。安裝別人寫的 Skill 前：

- **先打開 `SKILL.md` 和 `scripts/` 讀過一遍**，確認它會做什麼
- 特別留意 `allowed-tools`、刪除檔案、對外連網、讀取金鑰的指令
- 優先使用官方或星數高、近期有維護的來源
- 公司環境請遵守 IT 規範（Enterprise 方案可對上傳的 Skill 做安全掃描）

---

## 速查卡

```text
【Skill】怎麼做 —— 給 Claude 看的 SOP
  位置：~/.claude/skills/<名稱>/SKILL.md（個人）
        .claude/skills/<名稱>/SKILL.md（專案）
  必填：name、description（description 決定何時觸發，要寫清楚）
  呼叫：/名稱，或讓 Claude 自動判斷
  有副作用的動作：加 disable-model-invocation: true
  說明太長：拆到 reference 檔，SKILL.md 保持 500 行內

【Docs】做出來的東西 —— 給人看的活文件
  建立：「幫我寫一份○○文件」
  修改：對話中說，或在文件上留言送給 Claude
  分享：Share；匯出：文件名稱 → Export（Word / PDF / Markdown）

【判斷】
  每次都要記得的規則 → CLAUDE.md
  做某件事時才需要的流程 → Skill
  要給人看、一起改的成品 → Docs
```

---

## 參考資料

- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Using skills in Claude - Claude Help Center](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [anthropics/skills - GitHub](https://github.com/anthropics/skills)
- [Release notes - Claude Help Center](https://support.claude.com/en/articles/12138966-release-notes)
- [Anthropic is killing off Claude Cowork and folding it into Claude chat, launching Claude Docs and Claude Slides - VentureBeat](https://venturebeat.com/technology/anthropic-is-killing-off-cowork-and-folding-it-into-claude-launching-claude-docs-and-claude-slides)
