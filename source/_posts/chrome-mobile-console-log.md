title: 使用 Chrome 查看手機網頁 Console Log
author: Alan Liu
tags:
  - Chrome
  - Mobile
  - Console
  - ''
  - ''
categories:
  - Other
date: 2023-11-27 12:22:00
---
# 使用 Chrome 查看手機網頁 Console Log

## 前言

因案子使用者都使用手機開啟頁面，而手機上有一些意想不到的問題。

此教學是方便查看手機頁面 Console

## 開始

### 第一步、進入手機設定 [Android]

*# 如果已開啟 USB 偵錯，可跳過此步驟。* [點我跳至第二步](#第二步、進入-Chrome-inspect-頁面)

#### 1. 啟動開發者模式

手機進入設定 > 關於本機 > 版本資訊

點擊版本號碼 10 下，即可啟動開發者模式

![啟動開發者模式](186185.jpg)

開啟後接下一步

#### 2. 啟動 USB 偵錯

手機進入設定 > 其他設定 > 開發人員選項

![開發人員選項](186187.jpg)

找到 USB 偵錯並開啟

![啟動 USB 偵錯](186188.jpg)

開啟後即可進行下一步


### 第二步、進入 Chrome inspect 頁面

手機使用傳輸線接上電腦後

手機開啟chrome並進入想要觀看 Console 的網站

![網站](186184.jpg)

PC 網站網址輸入 `chrome://inspect/#devices`

![inspect 範例](1701015276776.jpg)

這裡可看到所有手機正在開啟的頁面

接著點選 Inspect 即可觀看頁面 Console

![進入頁面](1701015300744.jpg)

今天的教學就到此

### Thank you! :smile:


---

參考


- [Is it possible to open developer tools console in Chrome on Android phone?
](https://stackoverflow.com/questions/37256331/is-it-possible-to-open-developer-tools-console-in-chrome-on-android-phone)

- [Remote debug Android devices](https://developer.chrome.com/docs/devtools/remote-debugging/)