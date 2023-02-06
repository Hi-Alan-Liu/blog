title: IIS 架設 VUE 站台
author: Alan Liu
tags:
  - IIS
  - URL Rewrite
  - Vue Router
  - web.config
categories:
  - IIS
date: 2022-05-05 15:34:00
---
# IIS 架設 vue 站台


## 前言

待補!

---

## 準備

IIS

URL Rewrite
下載網址：https://www.iis.net/downloads/microsoft/url-rewrite

*安裝完畢後在進行下一步*

---

## 開始

### 第一步 建立web.config

為了讓 IIS 能夠配置 vue-router
我們需要一份 web.config

官方範例 [Vue Router](https://router.vuejs.org/guide/essentials/history-mode.html#internet-information-services-iis)

```xml=
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Handle History Mode and custom 404/500" stopProcessing="true">
          <match url="(.*)" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

並將此檔案儲存至 vue.js 專案的 public 底下
這樣一來，每次建置的時候就會自動將 web.config 產生至 dist 資料夾

![web.config 存至 public 底下](1651734577669_1.jpg)

### 第二步 建置專案

```npm
npm run build
```

![建置完成範例圖](1651734577669_2.jpg)

### 第三步 設定 IIS

![新增網站](1651734577669_3.jpg)

實體路徑需指定到佈署產生的 dist 資

如果沒有domain主機名稱可為空

設定完成後，即可測試 vue 站台是否架設成功

### Thank you! :smile:
---

參考

- [Vue Router](https://router.vuejs.org/guide/essentials/history-mode.html)
- [Hosting Vue JS SPA build on Microsoft IIS](https://www.linkedin.com/pulse/hosting-vue-js-spa-build-microsoft-iis-zainul-zain)