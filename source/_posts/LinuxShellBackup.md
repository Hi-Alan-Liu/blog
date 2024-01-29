title: Linux - Shell Script 備份檔案
author: Alan Liu
tags:
  - Palworld
  - Linux
  - Shell Script
  - Crontab
  - Tar
categories:
  - Linux
date: 2024-01-30 00:29:00
---
# Linux - Shell Script 備份檔案

## 前言

因遊玩幻獸帕魯需要定期備份伺服器

所以需要寫一個 shell 去進行備份檔案並壓縮檔案

## 開始

### 第一步、建立 Shell Script 檔案進行檔案備份

```Linux=
vi Palworld.sh
```

![建立 sh 檔案](1706546050051.jpg)

Palworld.sh 內容如下

```
#!/bin/bash
backup_path="/home/hialanliugood/Steam/steamapps/common/PalServer/Pal/Saved/SaveGames" # 備份路徑
folder_name="0" # 目標資料夾名稱
current_datetime=$(date +"%Y%m%d%H%M") # 取得當前日期和時間
backup_file="${folder_name}_${current_datetime}.tar.gz" # 壓縮檔名
full_path="${backup_path}/${folder_name}" # 完整路徑
finish_backup_path="${backup_path}/backup"
if [ -d "$full_path" ]; then
    cd "$full_path" || exit # 進入目標資料
    tar -czf "$backup_file" . # 壓縮目標資料夾內容到指定檔案
    mv "$backup_file" "$finish_backup_path" # 搬移壓縮檔位置
    cd - || exit # 切換回原始目錄
    echo "備份成功：${full_path} -> ${backup_file}"

    # 刪除過去3天以上的備份檔案
    find "$finish_backup_path" -name "${folder_name}_*.tar.gz" -type f -mtime +3 -exec rm {} \;
    echo "刪除過去3天以上的備份檔案完成"
else
    echo "錯誤：目標資料夾 ${full_path} 不存在"
fi
```

backup_path: 需備份的檔案絕對路徑

folder_name: 需要備份的資料夾

current_datetime: 取得日期

backup_file: 壓縮檔名

full_path: 備份壓縮檔儲存路徑


### 第二步、測試執行 Shell Script 檔案

直接輸入

```Linux=
./Palworld.sh
```

![執行 sh](1706546651028.jpg)

如果出現 `-bash: ./Palworld.sh: Permission denied`

代表權限不足，請輸入

```Linux=
chmod +x ./Palworld.sh
```

執行成功後的樣子

![成功範例](1706547415691.jpg)

### 第三步、使用 Crontab 執行排程

輸入以下指令

```Linux=
crontab -e
```

在最下方加入以下指令

此範例為一小時執行一次
```Linux=
0 * * * * /home/hialanliugood/Palworld.sh
```

![範例](1706547856773.jpg)

儲存方法為 Ctrl + X -> 接著按 Y -> 在按下 Enter

![接著按 Y](1706547865893.jpg)

![在按下 Enter](1706547875094.jpg)


### 第四步、檢查 Crontab 是否執行


```Linux=
crontab -l
```

![範例](1706547927784.jpg)

到這邊就可以自動進行備份囉!~

### 第五步、如果想要新增自動刪除

```Linux=
vi Palworld.sh
```

新增 刪除判斷

```Linux=
# 刪除過去3天以上的備份檔案
find "$finish_backup_path" -name "${folder_name}_*.tar.gz" -type f -mtime +3 -exec rm {} \;
echo "刪除過去3天以上的備份檔案完成"
```

![完整範例](1706548521298.jpg)


今天的教學就到此

### Thank you! :smile:


---

參考


- [使用Linux Shell Script，每天自動刪除或定期備份超過N天的日誌檔案](https://klab.tw/2023/02/use-linux-script-to-automatically-delete-files-every-day/)