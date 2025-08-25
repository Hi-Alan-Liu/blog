title: .NET - 新增資料庫索引
author: Alan Liu
tags:
  - dotnet
  - RDS
  - index
  - SQL
categories:
  - .NET
date: 2024-03-13 08:00:00
---
# .NET - 新增資料庫索引

## 前言

伺服器上線後，資料庫資料量逐漸變大，導致讀取速度變慢，

因此我們需要找出源頭並進行優化。

`此篇使用 AWS RDS 為例`

## 開始

### 使用尋找問題點

![RDS](1709890640120.jpg)

進入 RDS 查看資料庫負載，可明顯看出讀取資料時的負載量。

點選下方熱門 SQL 可以看到 RDS 幫我們將 DBLoad 算好

![SQL 陳述式](1709890737798.jpg)

找到問題的 SQL 陳述式

找到問題點就可以進入專案進行修改囉 ~

### 進入專案修改問題點

進入專案後依照 RDS 列出來的 SQL 陳述式，找到執行 SQL 的地方

![專案](1709890815324.jpg)

從範例可以看到Where `ApiAuthorizeId And Status` 以及 OrderBy `CreatedAt`

系統很常使用到此語法，所以我們須幫他新增索引

![新增索引](1709890852452.jpg)

```
    /// <summary>
    /// 建立索引
    /// </summary>
    /// <param name="modelBuilder"></param>
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
      modelBuilder.Entity<TableName>()
        .HasIndex(c => new { c.CreatedAt, c.Status, c.ApiAuthorizeId });
    }
```

新增後我們執行Migration指令

```
Add-migration AddIndexOnTableName
```

創建好 Migration 就行囉 ~

### 驗收並檢查

![MSSQL](1709893846968.jpg)

重新佈署專案後，可以到 SQL 查看是否新增索引

![RDS](1709893891517.jpg)

RDS 上也能看到更新後，資料庫負載明顯下降

這樣就優化完成囉 ~

今天的教學就到此

### Thank you! :smile:


---

參考


- [平均作用中工作階段](https://docs.aws.amazon.com/zh_tw/AmazonRDS/latest/UserGuide/USER_PerfInsights.Overview.ActiveSessions.html#USER_PerfInsights.Overview.ActiveSessions.AAS)