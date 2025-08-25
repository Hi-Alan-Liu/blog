title: Windows 排程器執行 Powershell
author: Alan Liu
tags:
  - Powershell
  - Windows
  - 工作排程器
categories:
  - Tool
date: 2022-10-11 14:37:00
---
# Windows 排程器執行 Powershell

## 開始

### 一、建立 PowerShell Scripts

建立給排程器執行的 Scripts

```PowerShell-Scripts=
start chrome https://www.google.com/
```

存儲時副檔名 `.ps1`

![儲存範例](1665473777407.jpg)

儲存後樣式

![成品](1665473777168.jpg)

其他 Scripts 範例

```PowerShell-Scripts=
$url = "https://test.api-server.com/api/v1/create_user"
$body = @{ name = 'alan liu' }
Invoke-WebRequest -Method 'POST' -Uri $url -Body $body -ContentType "application/json"
```

### 二、設定排程器

按下 Win+R，輸入 taskschd.msc 執行工作排程器

![輸入 taskschd.msc](1665480567365.jpg)

建立排程資料夾方便辨識

![建立資料夾](1665473777356.jpg)

建立基本工作

![建立基本工作](1665473777976.jpg)

輸入名稱及描述

![建立基本工作](1665482589027.jpg)

選擇排程週期；可依需求設定多久觸發一次

![觸發程序週期](1665482608020.jpg)

每天為例；設定觸發時間及間隔

![觸發程序](1665482627683.jpg)

設定觸發動作選擇 `啟動程式`

![動作](1665482637503.jpg)

程式或指令碼: powershell
新增引數: -ExecutionPolicy Bypass -File "檔案位置"

![啟動程式](1665482960714.jpg)

最後檢查是否設定皆正確

![完成](1665482977830.jpg)

基本上設定到這裡就完成囉 !

如果想要立即測試排程

可點擊右鍵執行

![執行](1665483018760_0.jpg)


今天的教學就到此

### Thank you! :smile:
---

參考


- [設定windows排程器執行powershell-script](https://ghostyguo.pixnet.net/blog/post/359754775-%E8%A8%AD%E5%AE%9Awindows%E6%8E%92%E7%A8%8B%E5%99%A8%E5%9F%B7%E8%A1%8Cpowershell-script)
- [工作排程器--Windows的忠實程序秘書](https://ithelp.ithome.com.tw/articles/10276390)