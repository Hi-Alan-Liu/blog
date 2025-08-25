title: .NET - Swagger 設定 Host
author: Alan Liu
tags:
  - Swagger
  - OpenAPi
  - Nginx
categories:
  - .NET
date: 2024-04-14 21:50:00
---
# .NET - Swagger 設定 Host

## 問題

因為雲端上的主機使用 Nginx 將請求轉發到不同 Docker 容器。

所以雲端上的 Swagger 預設連線網域會錯誤的，造成無法正常連線

以下是我的設定及錯誤訊息

![Nginx 設定](1713102062196.jpg)

![錯誤的Servers](1713102309197.jpg)

![連線錯誤的樣子](1713102331373.jpg)


## 解決辦法

### 設定 OpenApi Host

首先我們來到 Program.cs

設置 UseOpenApi 

```
var app = builder.Build();

app.UseOpenApi(options =>
{
    options.PostProcess = (context, request) =>
    {
        if (app.Environment.IsProduction())
        {
            context.Host = context.Host = "backend.******.***";
        };
    };
});

app.UseSwaggerUi3();
```

目前我是只有 Production 的環境會遇到這個問題

所以我新增了一個判斷

![Program.cs 設定方式](1713103212299.jpg)

設定後就完成囉 !

![完成的樣子](1713102516679.jpg)

今天的教學就到此

### Thank you! :smile:

---

參考

- [UI document Server URL cannot be change](https://github.com/RicoSuter/NSwag/issues/2914)