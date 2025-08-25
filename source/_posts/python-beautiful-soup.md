title: Python 使用 BeautifulSoup 解析頁面資料
author: Alan Liu
tags:
  - Python
  - BeautifulSoup
categories:
  - Python
date: 2023-04-19 16:50:00
---
# Python 使用 BeautifulSoup 解析頁面資料

## 準備

1.pip 安裝套件 BeautifulSoup 

```python=
pip install BeautifulSoup4
```

*準備完畢後在進行下一步*

---

## 開始

### 第一步、取得需解析的資料

引用 BeautifulSoup

並將須解析的資料填入

```python=
# start.py

from bs4 import BeautifulSoup

# 需要解析的資料
html = '<div class="store ...... 以下省略'

soup = BeautifulSoup(html, 'html.parser')
```

資料範例如下 :
```html=
<div class="store">
   <div class="store-info">
      <div class="store-name">
         <span class="flex-shrink-0">店名</span>
         <span class="font-bold">
            金玉堂
         </span>
      </div>
      <div class="address">
         <span class="flex-shrink-0">地址</span>
         <address class="not-italic">高雄市路竹區國昌路16號<a href="http://maps.google.com.tw/maps?q=高雄市路竹區國昌路16號" target="_blank" class="text-secondary">地圖</a></address>
      </div>
   </div>
   <div class="store-info">
      <div class="store-name">
         <span class="flex-shrink-0">店名</span>
         <span class="font-bold">
            統一超商
         </span>
      </div>
      <div class="address">
         <span class="flex-shrink-0">地址</span>
         <address class="not-italic">高雄市路竹區大社路162號<a href="http://maps.google.com.tw/maps?q=高雄市路竹區大社路162號" target="_blank" class="text-secondary">地圖</a></address>
      </div>
   </div>
</div>
```

### 第二步、依資料內容進行解析

```python=
# start.py

# 列表
contact_list = []

# 將每個店整理成各筆資料
store_infos = soup.find_all('div', class_='store-info')

for store_info in store_infos:
    # 解析店名
    store_name = store_info.find('span', class_='font-bold').text

    # 解析地址
    address = store_info.find('address').text

    # 解析地圖連結
    map_link = store_info.find('a')['href']

    # 建立字典
    contact_info = {
        'name': store_name,
        'address': address,
        'link': map_link
    }

    # 把字典放進列表
    contact_list.append(contact_info)

# 印出資料
print(contact_list)
```

撰寫完畢後執行即可取得解析後的資料

```cmd=
python start.py
```

### 補充、將資料輸出成 Excel

安裝套件

```cmd=
pip install pandas
pip install openpyxl  
```

引用套件並輸出檔案

```python=
# start.py

import pandas as pd
import json

***
中間省略
***

# 建立 DataFrame
df = pd.DataFrame(contact_list)

# 將 DataFrame 寫入 Excel 檔案
with pd.ExcelWriter('contact_list.xlsx') as writer:
    df.to_excel(writer, index=False, sheet_name='Sheet1')
```

執行後就可以輕鬆解析並取得Excel囉!

今天的分享就到此結束

### Thank you! :smile: