title: Arduino 自製天堂神器
author: Alan Liu
tags:
  - Arduino
  - Leonardo
  - LD3320
categories:
  - Arduino
date: 2022-08-16 18:15:00
---
# arduino 自製天堂神器

## 展示

<video src='d03dcb66e495.mp4' type='video/mp4' controls='controls' preload="auto" width='100%' height='100%'></video>

## 準備

Arduino Leonardo 1個

![Arduino Leonardo](1660930089422.jpg)
`必須要使用 Leonardo 才能使用虛擬鍵盤 Keyboard 指令`

`*UNO 不支援虛擬鍵盤 Keyboard`

LD3320 1個

![LD3320](1660930089430.jpg)

杜邦線 多條



## 開始
### 第一步、連接元件
依照圖示使用杜邦線將孔未連接

![杜邦線連接示意圖](1655197062093.png)

LD3320 連接方式 
3.3V  --  VCC

GND  --  GND

*MISO  --  MISO

*MOSI  --  MOSI

*SCK  -- SCK

NSS  --  D4

RST  --  D9

WR -- GND

IRQ -- D2

記號 * 的部分需要插置 ICSP 位置

![ICSP 對應圖](1655197062666.png)


### 第二步、程式碼撰寫

![Arduino IDE](1660939386997.jpg)

```Arduino=
#include <ld3320.h>
#include "Keyboard.h"

VoiceRecognition Voice;                         //聲明一個語音辨識對象

void setup(){
    Serial.begin(9600);
    Keyboard.begin();
    Keyboard.releaseAll();
    
    Voice.init();                               //初始化VoiceRecognition模組   
    Voice.addCommand("Chao Shou Di Ren Gong Ji", 0);
    Voice.addCommand("Ti Li Bu Zu Liao", 1);
    Voice.addCommand("Syu Yao Geng Duo De Yao Shuei", 2);
    Voice.addCommand("Syu Yao Jheng Li Yi Sia Bei Bao", 3);
    Voice.start();//開始辨識
}

void loop() {
    switch(Voice.read()){
        case 0:
            Keyboard.press('3');
            delay(50);
            Keyboard.releaseAll();
            Serial.println("遭受敵人攻擊了");
        break;
        
        case 1:
            Keyboard.press('4');
            delay(50);
            Keyboard.releaseAll();
            Serial.println("體力不足了");
        break;
        
        case 2:
            Keyboard.press('4');
            delay(50);
            Keyboard.releaseAll();
            Serial.println("需要更多的藥水");
        break;  
        
        case 3:
            Keyboard.press('4');
            delay(50);
            Keyboard.releaseAll();
            Serial.println("需要整理一下背包");
        break;  

        default:
        break;
    }
}
```

`addCommand`:  設定想要辨識的詞句 *拼音為主*

`Keyboard.press`:  設定虛擬鍵盤按下的按鈕

`Keyboard.releaseAll()`: 鬆開按下的按鈕

`delay`: 設定延遲時間


### 第三步、上傳程式碼

燒入前先檢查開發版是否選擇正確

![開發版檢查](1660930089960.jpg)

如果設定皆無誤即可按下上傳

![上傳按鈕範例](1660939547182.jpg)

上傳成功後即可立即測試指定語音是否能正常觸發按下按鈕囉 ~

今天的分享就到此結束

### Thank you! :smile:
---

參考

- [Arduino系列教程十：语音识别模块 LD3320](https://www.waveshare.net/study/article-11-1.html)
- [Arduino Leonardo控制板的數位14~17腳、類比A6~A11腳以及SPI腳位介紹](https://swf.com.tw/?p=406)
- [用Arduino Leonardo制作虚拟键盘(简易badusb)](https://blog.csdn.net/zbp_12138/article/details/103653009)