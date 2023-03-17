title: .NET  MiniExcel 匯入資料
author: Alan Liu
tags:
  - MiniExcel
  - Excel
  - dotnet
categories:
  - ' .NET'
date: 2023-03-17 16:50:00
---
# .NET MiniExcel 匯入資料

## 準備

1.NuGet 套件安裝 MiniExcel

![安裝 MiniExcel](1679037848159.jpg)

*準備完畢後在進行下一步*

---

## 開始

### 第一步、創建 ViewModel

創建與 Excel 相同欄位名稱的 ViewModel

![Excel 範例](1679038144989.jpg)

![創建 ViewModel](1679037963610.jpg)

* ExcelColumnName 需要與 Excel 欄位名稱相同

### 第二步、讀取 Excel 並塞入資料

Query 查詢 Excel 返回強型別 IEnumerable 資料

```C#=
var rows = MiniExcel.Query<ViewModel>(path);

// or

using (var stream = File.OpenRead(path))
    var rows = stream.Query<ViewModel>();
```

![獲得 Excel 資料](1679038627943.jpg)

這樣就可以獲得 Excel 內的資料

### 補充、將欄位相同的資料整理好

```C#=
var empoyeeList = rows.GroupBy(x => x.EmployeeId).Select(x => new
{
  EmployeeId = x.Key,
  Questions = x.Select(a => new { a.QuestionCode, a.Answer, a.ResponseAt })
}).ToList();
```

如此就能將相同 EmployeeId 的資料合併再一起。


今天的分享就到此結束

### Thank you! :smile:

---

參考

- [MiniExcel](https://github.com/mini-software/MiniExcel)