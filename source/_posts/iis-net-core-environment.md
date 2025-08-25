title: IIS - Asp Net corn 設定 Environment
author: Alan Liu
tags:
  - IIS
  - ASP.NET
  - Environment
  - web.config
categories:
  - IIS
date: 2023-11-22 17:59:00
---
# IIS - Asp Net corn 設定 Environment

## 前言

佈署於伺服器上的專案，都需要依據需求設定不同的環境。

此教學為在 IIS 上的設定方式


## 開始

### 方法一、進入 IIS 站台設定

進入 Internet Information Services (IIS) 管理員

點選需要設定的站台，選擇 設定編輯器

![選擇設定編輯器](1700475976275.jpg)

點選上方區段，選擇 aspNetCore

![選擇區段](1700476543766.jpg)

設定 environmentVariables 

![設定](1700476647073.jpg)

點選新增設定 name 、value

name : `ASPNETCORE_ENVIRONMENT`

value : `Testing`

依據環境設定 `Production`、`Staging`、`Testing`

![設定變數](1700476703339.jpg)

設定完畢重啟站台即可生效。

### 方法二、透過 dotnet publish 調整 Web.config

也可以透過指令變更環境

`-c` | `--configuration <CONFIGURATION>` : 建置配置，預設為 `Debug`

`-o` | `--output <OUTPUT_DIRECTORY>` : 設定輸出目錄路徑

`-p:<NAME>=<VALUE>` : 設定屬性

```
dotnet publish 'xxx.csproj' -c Release -p:EnvironmentName=Testing -o 'publish'
```

這樣就會產生出對應的 Web.config

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" arguments=".\ServerName.API.dll" stdoutLogEnabled="false" stdoutLogFile=".\logs\stdout" hostingModel="inprocess">
        <environmentVariables>
          <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Testing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

設定完畢重啟站台即可生效。

今天的教學就到此

### Thank you! :smile:


---

參考


- [如何透過 dotnet publish 調整 ASP․NET Core 部署到 IIS 的 Web.config 內容
](https://blog.miniasp.com/post/2023/03/30/How-to-set-environment-name-for-webconfig-when-run-dotnet-publish)

- [dotnet publish](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-publish)