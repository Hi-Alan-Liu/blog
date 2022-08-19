title: Arduino 自製天堂神器
author: Alan Liu
date: 2022-08-16 18:15:56
tags:
---
# arduino 自製天堂神器

## 展示

<video src='d03dcb66e495.mp4' type='video/mp4' controls='controls' preload="auto" width='100%' height='100%'></video>

## 準備

Arduino Leonardo 


## 開始


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
