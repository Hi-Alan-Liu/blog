---
title: 給美編的 Claude Code 教學：從參考圖到 Illustrator .ai 與 PNG
author: Alan Liu
tags:
  - Claude Code
  - AI
  - Illustrator
  - Design
categories:
  - Tool
date: 2026-11-17 10:00:00
---

美編同事問我：「聽說 Claude 可以幫忙做圖，那我要怎麼用？」我第一反應是 —— 它不是生圖 AI，但它會**寫 Illustrator 看得懂的東西**。這篇就用一個實際案例，帶你從終端機輸入第一句話開始，丟給 Claude 參考圖與設計想法，最後拿到一個**分好圖層的 .ai 檔**，以及一整包 **1x / 2x / 3x 的透明 PNG**。

<!-- more -->

> 本篇跳過安裝，假設你已經能在終端機輸入 `claude` 啟動 Claude Code。指令細節可參考 [Claude Code 常用指令教學](/claude-code-commands)。
>
> 文中穿插工程師「阿哲」與美編「小芸」的對話，是這篇教學實際討論時的片段。

---

## 先講清楚：Claude 能做什麼、不能做什麼

> 💬 **小芸**：所以我丟一張圖，它就會畫一張新的給我？
> 💬 **阿哲**：不是喔。Claude 「看得懂」圖片，但它不是 Midjourney 那種直接吐出點陣圖的生圖模型。它做的是**把你的想法翻譯成向量指令**，讓 Illustrator 去畫。

| 適合交給 Claude | 不適合交給 Claude |
| --- | --- |
| 幾何風格的圖示、Logo 草案、資訊圖表 | 手繪質感插畫、寫實人物 |
| 一次建好多個畫板、圖層、色票 | 需要「感覺對了」的細膩筆觸 |
| 批次輸出多尺寸 PNG、重新命名 | 取代你的美感判斷 |
| 照品牌規範反覆微調 | 處理字型授權問題 |

一句話：**Claude 是一個很會寫腳本、聽得懂設計語言的助理**，最後的美感把關還是你。

---

## 終端機 3 分鐘入門（給沒碰過的人）

> 💬 **小芸**：我看到黑色視窗就緊張。
> 💬 **阿哲**：你只需要會三件事：打開、進到資料夾、輸入 `claude`。其他都用中文跟它講就好。

1. **在資料夾直接開終端機**：在檔案總管中，對專案資料夾按右鍵 →「在終端機中開啟」（Mac 是在 Finder 對資料夾按右鍵 →「服務」→「新增位於檔案夾位置的終端機視窗」）。
2. **確認位置**：視窗前面顯示的路徑，就是你目前所在的資料夾。
3. **輸入 `claude` 按 Enter**：看到對話框就可以開始打字了。

離開時輸入 `/exit`，或按兩次 `Ctrl + C`。

### 先把資料夾整理好

Claude 只看得到你所在資料夾裡的東西，所以先準備好這樣的結構（**資料夾名稱建議用英文**，後面會說為什麼）：

```text
coffee-icons/
├── ref/          ← 參考圖、手稿照片、mood board
├── scripts/      ← Claude 寫的 Illustrator 腳本會放這
├── output/       ← .ai 與 PNG 輸出位置
└── CLAUDE.md     ← 品牌規範（下一步會寫）
```

---

## Step 1：用 CLAUDE.md 寫下品牌規範

每次都重新交代主色、線條粗細很累。把規範寫在 `CLAUDE.md`，Claude Code 每次啟動都會自動讀它，等於一份**常駐的設計 brief**。

```markdown
# 白鷺咖啡 圖示專案

## 品牌規範
- 主色 #6B4226（咖啡棕）、輔色 #F2E6D8（奶泡白）、強調色 #D98E04（焦糖）
- 風格：線性圖示，圓角端點、圓角轉折
- 畫板 512×512 px，筆畫 24 px，四周保留 48 px 安全區
- 背景透明

## 技術規則
- Illustrator 腳本使用 ExtendScript（ES3 語法）：不要用 let、const、箭頭函式
- 一個圖示 = 一個畫板 + 一個圖層，名稱格式 icon-英文小寫（例：icon-latte）
- 輸出：output/coffee-icons.ai，PNG 放 output/png/，含 1x、@2x、@3x
```

> 💬 **阿哲**：最後那段「技術規則」是我加的。Illustrator 的腳本引擎很老，不先講清楚，Claude 偶爾會寫出新版 JavaScript 語法然後執行失敗。
> 💬 **小芸**：所以上半段我負責、下半段你負責，分工剛好。

不想自己寫也可以直接說：「幫我建立 CLAUDE.md，主色是 #6B4226……」讓 Claude 產生，你再修。

---

## Step 2：把參考圖丟給 Claude

給圖片有三種方式，挑順手的：

| 方式 | 做法 |
| --- | --- |
| **拖曳** | 把圖片檔直接拖進終端機視窗，會自動帶入檔案路徑 |
| **寫路徑** | 圖片放在 `ref/` 裡，對話中寫 `ref/sketch.jpg` 即可 |
| **貼上截圖** | 截圖後在 Claude Code 內貼上（Mac 為 `Ctrl + V`，Windows 為 `Alt + V`） |

### 先讓它「說出」看到什麼，再動手

這是我最推薦的習慣：**不要一丟圖就叫它畫**，先確認它理解得跟你一樣。

```text
ref/sketch.jpg 是我手畫的四個圖示草稿，ref/mood.png 是風格參考。
先不要寫任何檔案，請用條列描述：
1. 你看到的四個圖示分別是什麼
2. 參考圖的風格特徵（線條、圓角、留白、配色）
3. 你打算怎麼在 512×512 畫板上構圖
```

Claude 回覆後，像跟同事對稿一樣修正它：「第三個不是茶壺，是手沖壺，壺嘴要細長」。**這一步花 2 分鐘，可以省掉後面 20 分鐘來回。**

---

## Step 3：生成 .ai 檔 —— 兩條路線

> 💬 **小芸**：它到底怎麼把東西「放進」Illustrator？
> 💬 **阿哲**：兩種方法。簡單的是讓它畫 SVG，你自己打開存檔；進階的是讓它寫 Illustrator 腳本，一次把畫板、圖層、色票、輸出全部做完。

### 路線 A：SVG（最簡單，適合單一圖示）

```text
依照剛剛的描述，把「拿鐵杯」畫成 SVG，存到 output/icon-latte.svg。
每個部件用 <g id="..."> 分組並取英文名稱。
```

1. 用瀏覽器打開 `.svg` 快速預覽。
2. 在 Illustrator：**檔案 → 開啟** 該 SVG。
3. **檔案 → 另存新檔**，格式選 **Adobe Illustrator (*.AI)**。

SVG 裡的 `id` 會變成 Illustrator 中的物件名稱，圖層面板一樣看得懂。缺點是多畫板、色票、批次輸出都要你手動處理。

### 路線 B：Illustrator 腳本（推薦，適合一整組）

```text
寫一支 Illustrator 腳本 scripts/make-icons.jsx：
- 建立 4 個 512×512 畫板，分別是 latte、pour-over、bean、cake
- 每個圖示一個圖層，把品牌三色加進色票
- 存成 output/coffee-icons.ai
- 再把每個畫板輸出成 1x、@2x、@3x 透明 PNG 到 output/png/
```

Claude 寫出來的腳本大致長這樣（節錄，實際會更完整）：

```javascript
#target illustrator
// make-icons.jsx — 由 Claude Code 產生

var ROOT = "D:/design/coffee-icons/output/";
var NAMES = ["icon-latte", "icon-pour-over", "icon-bean", "icon-cake"];

function hex(h) {
    var c = new RGBColor();
    c.red = parseInt(h.substr(1, 2), 16);
    c.green = parseInt(h.substr(3, 2), 16);
    c.blue = parseInt(h.substr(5, 2), 16);
    return c;
}

// 1. 建立文件：RGB、4 個 512×512 畫板橫向排列
var doc = app.documents.add(DocumentColorSpace.RGB, 512, 512, 4,
                            DocumentArtboardLayout.Row, 40);

// 2. 品牌色票
var brand = { "咖啡棕": "#6B4226", "奶泡白": "#F2E6D8", "焦糖": "#D98E04" };
for (var k in brand) {
    var sw = doc.swatches.add();
    sw.name = k;
    sw.color = hex(brand[k]);
}

// 3. 每個畫板命名、建立對應圖層並繪製
for (var i = 0; i < NAMES.length; i++) {
    doc.artboards[i].name = NAMES[i];
    var layer = doc.layers.add();
    layer.name = NAMES[i];
    // drawIcon(layer, doc.artboards[i].artboardRect, NAMES[i]);  ← 各圖示的繪製函式
}

// 4. 存成 .ai
doc.saveAs(new File(ROOT + "coffee-icons.ai"), new IllustratorSaveOptions());

// 5. 每個畫板輸出 1x / 2x / 3x 透明 PNG
var pngDir = new Folder(ROOT + "png");
if (!pngDir.exists) pngDir.create();

var scales = [1, 2, 3];
for (var a = 0; a < doc.artboards.length; a++) {
    doc.artboards.setActiveArtboardIndex(a);
    for (var s = 0; s < scales.length; s++) {
        var opt = new ExportOptionsPNG24();
        opt.artBoardClipping = true;   // 只輸出畫板範圍
        opt.transparency = true;       // 透明背景
        opt.antiAliasing = true;
        opt.horizontalScale = opt.verticalScale = 100 * scales[s];
        var suffix = scales[s] === 1 ? "" : "@" + scales[s] + "x";
        doc.exportFile(new File(pngDir + "/" + NAMES[a] + suffix + ".png"),
                       ExportType.PNG24, opt);
    }
}
```

> 💬 **小芸**：程式碼我看不懂耶。
> 💬 **阿哲**：不用看懂。你只要知道每一段註解在做什麼，要改的時候用中文跟 Claude 說「把畫板改成 1080×1080」就好。

### 執行腳本

**方法一：在 Illustrator 裡手動執行**

**檔案 → 指令碼 → 其他指令碼**（快捷鍵 `Ctrl + F12`），選擇 `scripts/make-icons.jsx`。

**方法二：直接請 Claude 幫你執行**

Illustrator 開著的狀態下，對 Claude 說：

```text
幫我用 Illustrator 執行 scripts/make-icons.jsx
```

在 Windows 上，它會透過 PowerShell 呼叫 Illustrator：

```powershell
$ai = New-Object -ComObject Illustrator.Application
$ai.DoJavaScriptFile("D:\design\coffee-icons\scripts\make-icons.jsx")
```

Mac 則是透過 AppleScript：

```bash
osascript -e 'tell application "Adobe Illustrator" to do javascript file "/Users/you/coffee-icons/scripts/make-icons.jsx"'
```

> 💬 **阿哲**：第二種最爽。你講一句話，Illustrator 自己跳出來畫完、存檔、輸出，你只要去 `output/` 收成果。

---

## Step 4：看結果、用「設計語言」來回修

第一版通常有七成像。這時候**用你平常對稿的講法就行**，Claude 聽得懂設計術語：

```text
看 output/png/icon-latte@2x.png：
- 杯子視覺重心偏下，往上移 16px 做光學置中
- 拉花太複雜，簡化成一個愛心
- 杯耳的筆畫看起來比杯身粗，統一成 24px
改完重新執行腳本。
```

幾個好用的回饋技巧：

- **指名檔案**：說「看 `icon-bean@2x.png`」，讓它重新讀圖比對，而不是憑記憶修。
- **一次講 2～4 點**：太多點容易漏，太少則來回次數變多。
- **給數字**：「往上一點」不如「往上 16px」。
- **整組比較**：「把四張 PNG 並排看，哪一個的線條密度跟其他不一致？」它會自己挑出問題。
- **滿意的版本先存檔**：「把目前的 make-icons.jsx 複製一份成 make-icons-v2.jsx」，改壞了隨時退回。

最後一哩 —— 某條曲線的弧度、某個角度的手感 —— **直接在 Illustrator 裡用滑鼠調**，通常比用文字描述快。Claude 負責把 80% 的骨架與重複工作做完，最後 20% 交給你的手。

---

## Step 5：只要 PNG？單獨批次輸出

如果 `.ai` 是你自己畫的，只想讓 Claude 處理繁瑣的輸出，也可以：

```text
寫一支腳本 scripts/export-png.jsx：
對目前 Illustrator 開啟中的文件，
把每個畫板依畫板名稱輸出成 1x、@2x、@3x 透明 PNG 到 output/png/。
```

之後每次改完設計，按一次 `Ctrl + F12` 執行這支腳本就好，**再也不用一個一個「轉存」**。這支腳本可以留著，下個專案繼續用。

---

## 常見問題排解

| 狀況 | 原因 | 解法 |
| --- | --- | --- |
| 腳本跳出 `SyntaxError` | 用了新版 JS 語法（`let`、`=>`） | 在 `CLAUDE.md` 註明 ES3，或直接把錯誤訊息貼給 Claude |
| 找不到檔案、存檔失敗 | 路徑含中文或空白 | 資料夾與檔名改用英文、不含空白 |
| PowerShell 執行沒反應 | Illustrator 沒開，或有對話框擋住 | 先開好 Illustrator、關掉所有彈出視窗 |
| PNG 背景是白的 | 未開啟透明 | 確認 `transparency = true`，且沒有白色底圖物件 |
| 輸出的 PNG 範圍不對 | 輸出的是整份文件而非畫板 | 確認 `artBoardClipping = true` |
| 開檔後字型被替換 | 印刷/交件環境沒有該字型 | 交件前請 Claude 在腳本中加上「文字建立外框」 |
| 顏色跟印刷稿不同 | 文件是 RGB | 印刷用途在 `CLAUDE.md` 註明使用 CMYK |

遇到任何錯誤，**最快的方法就是把錯誤訊息整段貼給 Claude**，說「執行出現這個錯誤，幫我修」。

---

## 速查卡

| 我想要… | 對 Claude 說… |
| --- | --- |
| 確認它看懂參考圖 | 「先不要寫檔案，描述你在 `ref/xxx.jpg` 看到什麼」 |
| 快速出一個圖示 | 「畫成 SVG，存到 `output/xxx.svg`」 |
| 一次建好一整組 .ai | 「寫 Illustrator 腳本，建 N 個畫板、圖層、色票，存成 .ai」 |
| 直接執行 | 「幫我用 Illustrator 執行 `scripts/xxx.jsx`」 |
| 微調 | 「看 `xxx@2x.png`，○○ 往上 16px、線條統一 24px，改完重跑」 |
| 批次出圖 | 「把每個畫板輸出 1x / @2x / @3x 透明 PNG」 |
| 修錯誤 | 直接貼上錯誤訊息 |

---

## 結語

> 💬 **小芸**：做完才發現，我其實一直在做我擅長的事 —— 看稿、挑毛病、下判斷。
> 💬 **阿哲**：對，畫板、圖層、輸出這些「手很忙但腦不用動」的事，就丟給它。

Claude Code 對美編最大的價值，不是取代你畫圖，而是把**規範化、重複性、批次性**的工作接過去。第一次可能要花點時間習慣終端機，但只要那支輸出腳本存下來，往後每個專案都能省下一大段時間。

---

## 附錄：這篇文章是怎麼寫出來的

這篇教學本身也用了多角色迭代的方式產生：由 AI 工程師與美編討論出初稿，再請兩位大學生（設計系小晴、資工系阿凱）試讀給意見，共跑三輪。

| 輪次 | 學生意見 | 調整內容 |
| --- | --- | --- |
| 第一輪 | 小晴：「看到終端機就卡住，`cd` 是什麼？也沒有實際能照抄的句子。」<br>阿凱：「SVG 轉 .ai 會不會丟失圖層？為什麼不直接寫 Illustrator 腳本？也該講 Claude 為什麼不能直接生圖。」 | 新增「能做／不能做」對照與「終端機 3 分鐘入門」；每一步都附可照抄的提示詞；拆成 SVG 與腳本兩條路線 |
| 第二輪 | 小晴：「需要一個從頭到尾的完整案例；做出來不好看要怎麼改？」<br>阿凱：「能不能不用手動點選單就執行腳本？中文路徑、語法錯誤怎麼辦？」 | 全文改以「白鷺咖啡圖示組」貫穿；新增 Step 4 回饋技巧；補上 PowerShell / AppleScript 執行方式與問題排解表 |
| 第三輪 | 小晴：「內容夠了，但想要一張最後能截圖貼在桌面的速查表。」<br>阿凱：「提醒交件時字型與色彩模式的坑。」 | 新增速查卡；排解表補上文字外框與 CMYK 兩列 |
