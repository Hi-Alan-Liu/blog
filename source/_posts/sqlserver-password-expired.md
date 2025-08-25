title: SQL Server 密碼過期問題處理 
author: Alan Liu 
tags: 
  - SQLServer 
  - Database
categories: 
  - Database 
date: 2025-08-25 11:30:00 
---
# SQL Server 密碼過期問題處理

### 前言

在使用 API 連線至資料庫時，若遇到以下錯誤：

``` 
Login failed for user 'howtalent'.  Reason: The password of the account has expired.
```

![錯誤訊息範例](1756095053153.jpg)

代表該資料庫帳號的密碼已經過期，必須更新或取消密碼過期規則。

### 原因

SQL Server 在建立登入帳號時，若勾選了 **「強制執行密碼原則」(Enforce password policy)** 與 **「強制執行密碼過期」(Enforce password expiration)** ，則密碼會有存活期限，過期後無法再登入。

### 解決方式

#### 方法一：取消密碼過期規則

1.  進入 SQL Server Management Studio (SSMS)
2.  找到登入帳號（例：`howtalent`）
3.  編輯帳號屬性

![找到登入帳號](1756092089667.jpg)

4.  取消勾選：
    -   ✅ 強制執行密碼原則 (Enforce password policy)
    -   ✅ 強制執行密碼過期 (Enforce password expiration)

![取消勾選](1756092046716.jpg)

#### 方法二：修改帳號密碼

透過 SQL 指令直接更新帳號密碼：

``` sql
ALTER LOGIN howtalent WITH PASSWORD = 'T7p#x2Lq!' UNLOCK;
```

-   `howtalent`：登入帳號名稱
-   `T7p#x2Lq!`：新密碼（可維持與原本相同，除非 SQL Server 強制需更換）

如果只想維持舊密碼，直接用同一組即可。


### Zeabur 密碼位置

在 Zeabur 部署的 MSSQL 資料庫，`SA` 密碼儲存在 **變數設定 (Variable)** 裡：
- Key: `MSSQL_SA_PASSWORD`
- Value: `T7p#x2Lq!`

![Zeabur 密碼位置](1756092168443.jpg)


### Thank you! :smile:


參考
- [ALTER LOGIN (Transact-SQL) - Microsoft
Docs](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-login-transact-sql)
