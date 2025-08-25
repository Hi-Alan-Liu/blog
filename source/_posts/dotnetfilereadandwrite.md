title: .NET - 本地檔案讀取及寫入
author: Alan Liu
tags:
  - dotnet
  - JsonSerializer
  - Directory
  - File
categories:
  - .NET
date: 2024-02-16 08:00:00
---
# .NET - 本地檔案讀取及寫入

## 前言

待補

## 開始

### 一、讀取檔案

```C#=
  var path = Directory.GetCurrentDirectory();
  var filePath = $"Assets/User/Setting.json";
  var text = File.ReadAllText(Path.Combine(path, filePath));
```

如果檔案內容為 JSON 可使用 JsonSerializer 進行轉換

```C#=
  JsonSerializer.Deserialize<TValue>();
```

### 二、寫入檔案


```C#=
  var jsonData = '儲存的內容';
  var filePath = $"Assets/User/Setting.json";
  File.WriteAllText(filePath, jsonData);
```

也可以在建立前，新增判斷檢查資料夾是否建立

```C#=
  var directoryPath = $"Assets/User";
  // 檢查資料夾是否建立
  if (!Directory.Exists(directoryPath))
  {
     Directory.CreateDirectory(directoryPath);
  }
```

今天的教學就到此

### Thank you! :smile:

