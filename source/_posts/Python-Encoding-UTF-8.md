title: 執行 Python 中文亂碼問題
author: Alan Liu
tags:
  - Python
  - Encoding
  - UTF-8
categories:
  - Python
date: 2023-11-01 18:01:00
---
# 執行 Python 中文亂碼問題

## 前言

Python 執行指令時偵測到中文，導致執行指令中斷無法正常運作。

![指令中斷範例](1698832227142.jpg)

## 開始

其實解法很簡單，只需要在檔案最上方加上 encoding:utf-8

```python=
# encoding:utf-8
```

![encoding範例](1698832334040.jpg)


這樣就可以正常運行囉 !

今天的分享就到此結束

### Thank you! :smile: