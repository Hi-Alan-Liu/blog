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

用 Claude 一陣子後，常會遇到兩種煩惱：「同樣的事每次都要重新交代一遍」，以及「做出來的文件散落在對話裡，很難跟同事一起改」。**Skill** 解決前者，**Claude Docs** 解決後者。這篇整理兩者的觀念、附上實際範例，以及去哪裡找別人寫好的 Skill。

<!-- more -->

> 本篇假設你已會基本操作 Claude Code，還不熟的話可以先看 {% post_link claude-code-commands 'Claude Code 常用指令教學' %}。

---

## 一句話分清楚

| | Skill | Claude Docs |
| --- | --- | --- |
| 是什麼 | 一份**可重複使用的工作說明書** | 一份**可以一起編輯的線上文件** |
| 本體 | 一個資料夾，裡面有 `SKILL.md` | claude.ai 上的一個連結 |
| 給誰看 | 給 Claude 看，讓它照著做 | 給人看：你、同事、主管 |
| 比喻 | 新人的 SOP 手冊 | 團隊共用的 Google 文件 |

再加上大家熟悉的 `CLAUDE.md`，判斷放哪裡很簡單：

| 內容 | 放哪裡 |
| --- | --- |
| 每次對話都要遵守的規則 | `CLAUDE.md`（每次一定讀） |
| 做某件事時才需要的流程 | **Skill**（需要時才讀） |
| 要給人看、要一起改的成品 | **Docs** |

---

## Skill：給 Claude 的 SOP

### 結構

Skill 就是一個資料夾，最少只需要一個 `SKILL.md`；說明太長時，可以把細節拆到其他檔案：

```text
weekly-report/
├── SKILL.md          ← 必要：流程本體
├── template.md       ← 選用：參考文件，需要時才讀
└── scripts/
    └── collect.py    ← 選用：可執行的腳本
```

`SKILL.md` 上面是基本資料（frontmatter），下面是操作步驟：

```markdown
---
name: weekly-report
description: 撰寫團隊週報。當使用者提到「週報」「本週進度」時使用。
---

1. 讀取本週的 git log
2. 依照 template.md 的格式整理成：本週完成、進行中、下週計畫
3. 每段最多 5 點，每點一句話
```

### 關鍵觀念：需要時才載入

Claude 平常**只看每個 Skill 的 `name` 和 `description`**，判斷用得上時才讀取 `SKILL.md` 全文，本文提到的參考檔也是真的需要才讀。所以裝幾十個 Skill 也幾乎不佔空間。

也因此，**`description` 是整個 Skill 最重要的一行**：寫清楚「做什麼、什麼時候用」，否則 Claude 永遠不會想到要用它。

### 觸發方式與存放位置

觸發方式有兩種：輸入 `/weekly-report` 主動呼叫，或直接說「幫我寫這週的週報」讓 Claude 自動判斷。可以用 frontmatter 限制：

| 設定 | 效果 | 適合 |
| --- | --- | --- |
| （預設） | 你和 Claude 都能觸發 | 大部分情況 |
| `disable-model-invocation: true` | 只有你能呼叫 | 部署、發布等**有副作用**的動作 |
| `user-invocable: false` | 只有 Claude 會用 | 純背景知識，例如命名規範 |

存放位置：個人用放 `~/.claude/skills/<名稱>/`，專案共用放 `.claude/skills/<名稱>/` 並 commit 進 repo。claude.ai 網頁版則在 **Customize → Skills** 管理，或把資料夾壓成 ZIP 上傳。

> 以前的自訂 slash command（`.claude/commands/xxx.md`）已併入 Skill，舊檔仍可用。

---

## 範例：這個部落格的「新增文章」Skill

我寫文章時遇過站內連結 404 的問題（網站在 `/blog/` 底下，手寫 `/xxx` 連結會少了路徑）。這種「每次發文都要注意」的規則，正適合寫成 Skill。以下是本部落格 repo 中實際使用的 `.claude/skills/new-post/SKILL.md`：

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
5. 寫完執行 `npx hexo generate` 確認沒有錯誤；若 build 讓 `package.json` 的 hexo 版本號被改動，用 `git checkout -- package.json` 還原

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
- 篇幅精簡，全文控制在約 200 行內：只寫讀者需要的步驟，不重複說明、不放讀者用不到的細節
- 不要自動 commit，等使用者確認
````

之後輸入 `/new-post Windows 印表機共用設定教學`，Claude 就會照這份規則產出文章。

> 💡 不想從零寫？Anthropic 官方的 `skill-creator` Skill 可以把你描述的流程直接做成 Skill。

---

## Claude Docs：給人看的活文件

Claude Docs 是 2026 年 9 月推出的功能。過去請 Claude 寫文件，成品是對話中的一大段文字，要修改就得重新產生整份。Docs 則是：

- 文件存在 claude.ai 上的**一個連結**，像 Google 文件一樣
- Claude 修改時**直接編輯原文**，不是重寫整份
- 同事可以**留言**，也能在留言中請 Claude 修改

| 想做的事 | 怎麼做 |
| --- | --- |
| 建立 | 在對話中說「幫我寫一份部落格改版提案**文件**」 |
| 修改 | 在對話中說要改什麼，或在文件上選取文字 → 留言 → 送給 Claude |
| 分享 | 文件上方 **Share** → 選擇組織內的人，給予檢視、留言或編輯權限 |
| 匯出 | 點文件名稱 → **Export** → Word、PDF、Markdown，或複製到 Google Docs、Notion |
| 找回 | claude.ai 的 **Artifacts** 列表依標題找；舊版本在文件名稱選單的版本紀錄 |

在 Claude Code 中也能用：說「把這次的調查結果寫成一份文件」，它會建立 Doc 並回傳連結，不會在專案裡多出檔案。一次性的問答，或屬於程式碼一部分的文件（例如 README），就不需要用 Docs。

---

## 兩者搭配

1. 用 **Skill** 規定週報的資料來源與格式，並在最後一步寫「寫成一份 Claude Doc」
2. 每週說一句「寫這週的週報」，Claude 自動套用 Skill
3. 成品進到 **Doc** 分享給團隊，同事留言、Claude 依留言修正
4. 定稿後匯出 PDF 存檔

**Skill 讓品質穩定，Docs 讓協作順暢。**

---

## 去哪裡找別人寫好的 Skill？

| 來源 | 說明 |
| --- | --- |
| [anthropics/skills](https://github.com/anthropics/skills) | **官方** repo，含 Word、Excel、PDF 等文件 Skill 與 `template/` 範本，學寫法最好的參考 |
| Claude Code 的 `/plugin` | 輸入 `/plugin marketplace add anthropics/skills` 後，用 `/plugin` 瀏覽安裝 |
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 社群整理的大型清單 |
| [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) | 偏 Claude Code 的社群清單，附安全性說明 |
| {% post_link claude-code-gstack 'gstack 多角色開發工作流程' %} | 我之前介紹過的大型 Skill 套件 |

⚠️ Skill 可以附帶**會被執行的腳本**。安裝別人寫的 Skill 前，先讀過 `SKILL.md` 與 `scripts/`，特別留意 `allowed-tools`、刪除檔案、對外連網的指令，並優先選擇官方或有持續維護的來源。

---

## 參考資料

- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Using skills in Claude - Claude Help Center](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [Release notes - Claude Help Center](https://support.claude.com/en/articles/12138966-release-notes)
- [Anthropic launches Claude Docs and Claude Slides - VentureBeat](https://venturebeat.com/technology/anthropic-is-killing-off-cowork-and-folding-it-into-claude-launching-claude-docs-and-claude-slides)
