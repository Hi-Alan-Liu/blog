title: FFmpeg 轉檔及合併音訊
author: Alan Liu
tags:
  - FFmpeg
  - filter
categories:
  - Tool
date: 2023-02-06 08:00:00
---
# FFmpeg 轉檔及合併音訊


## 開始


### 安裝 FFmpeg (Linux)

[FFmpeg 官方網址] (https://www.ffmpeg.org/)

更新 packages

```cmd=
$ sudo apt update
```

安裝 ffmpeg

```cmd=
$ sudo apt-get install ffmpeg
```

檢查 ffmpeg 是否成功，成為則會回應 ffmpeg 當前版本

```cmd=
$ ffmpeg -version
```

*安裝完畢後在進行下一步*

### 轉檔

將音訊檔 m4a 轉為 mp3

```cmd=
ffmpeg -i demo_1.m4a output.mp3
```

`-i demo_1.m4a`：為需轉檔的檔案 *需輸入檔案位置，如與指令同層則不需完整位置

`output.mp3`：為輸出的檔案名


### 合併音訊

將 2 個音訊拼湊

```cmd=
ffmpeg -i demo_1.mp3 -i demo_2.mp3 -filter_complex "[0:a][1:a]concat=n=2" demo_final.mp3
```

產生出來的檔案會先撥放 `demo_1.mp3` 接著撥放 `demo_2.mp3` 

如果需拼湊 2 個以上的音訊 只需要將 `concat=n=2` 中的 2 改為拼湊數量即可

拼湊 3 個音訊 Demo

```cmd=
ffmpeg -i demo_1.mp3 -i demo_2.mp3 -i demo_3.mp3 -filter_complex "[0:a][1:a]concat=n=3" demo_final.mp3
```

### 重疊音訊

將 2 個音訊合併

```cmd=
ffmpeg -i demo_1.mp3 -i demo_2.mp3 -filter_complex "[0:a] [1:a] amerge=inputs=2[aout]" -map "[aout]" -ac 2 demo_final.mp3
```

這個效果會將兩個音訊重疊同時播放

---

這次的分享主要是以音訊為主

在後再來分享影音的部分

### Thank you! :smile:
