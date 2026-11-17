---
name: new-post
description: 在此 Hexo 部落格新增一篇文章。當使用者說「寫一篇文章」「新增文章」「發一篇教學」時使用。
argument-hint: [文章主題]
---

# 新增部落格文章

主題：$ARGUMENTS

## 步驟
1. 檔名用英文小寫加連字號，放在 `source/_posts/<slug>.md`
2. 圖片放在同名資料夾 `source/_posts/<slug>/`，文章內直接寫檔名引用，例如 `![說明](image.png)`
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
- 不要自動 commit，等使用者確認
