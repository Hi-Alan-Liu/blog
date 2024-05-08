title: 解決 SQLServer 憑證鏈結不受信任問題
author: Alan Liu
tags:
  - SQL Server
  - Certificate
categories:
  - ' Other'
date: 2024-05-08 17:46:00
---
# 解決 SQLServer 憑證鏈結不受信任問題

## 問題

在運行本地資料庫的時候，出現了不受信任的錯誤連線訊息

![錯誤訊息範例](1714984424877.jpg)


## 解決辦法

我們只需要到 `其他連線參數` 設定參數就行了

先點選右下角選項 >> 再點選其他連線參數
![手動設定選項](1714984451088.jpg)

接著將以下參數填入

```
TrustServerCertificate=true
```

![填入參數範例](1714984484134.jpg)

這樣就可以正常連線囉 !

今天的教學就到此

### Thank you! :smile:

---

參考

- [SqlClient 疑難排解指南](https://learn.microsoft.com/zh-tw/sql/connect/ado-net/sqlclient-troubleshooting-guide?view=sql-server-ver16)