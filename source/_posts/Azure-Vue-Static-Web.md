title: 解決 Azure - Vue 靜態網站 404 問題
author: Alan Liu
tags:
  - Vue.js
  - Azure
  - router
  - saticwebapp
categories:
  - Vue.js
date: 2023-05-16 17:34:00
---
# 解決 Azure - Vue 靜態網站 404 問題

## 準備

[Azure 官方網址] (https://portal.azure.com/)

*安裝完畢後在進行下一步*

---

## 開始

### 問題

佈署於 `Azure - 靜態 Web 應用程式` 的 Vue 專案

再切換頁面時會出現 `404 : not found` 的問題

![404 : not found](1684226521857.jpg)


### 解決方式

再 Vue 專案內建立 `staticwebapp.config.json` 檔案

`staticwebapp.config.json` 是 Azure 靜態 Web 應用程式 的配置文件，用於定義路由和其他設定，以確保 Vue.js 應用程式在 Azure Static Web Apps 上正確運作。

```CMD=
# staticwebapp.config.json

{
  "navigationFallback": {
      "rewrite": "index.html"
  },
  "mimeTypes": {
      ".json": "text/json"
  }
}
```

![staticwebapp.config.json 檔案位置](1684227841490.jpg)


### 原因


Vue.js 是一個單頁應用程式框架，使用 Vue Router 來處理客戶端的路由。

在 Azure Static Web Apps 上部署 Vue.js 應用程式時，伺服器需要知道如何處理這些路由。

以確保所有路由都指向 index.html 檔案，讓 Vue Router 負責處理路由。


今天的分享就到此結束

### Thank you! :smile:

---

參考


- [azure-fallback-routes](https://learn.microsoft.com/en-us/azure/static-web-apps/configuration#fallback-routes)
- [Azure — How To Configure fallback routes for Vue Static Web Apps](https://medium.com/bb-tutorials-and-thoughts/azure-how-to-configure-fallback-routes-for-vue-static-web-apps-97b36026a33b)