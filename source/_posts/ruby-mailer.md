title: Ruby 信件功能及本地瀏覽
author: Alan Liu
tags:
  - ruby
  - mailer
categories:
  - Ruby on Rails
date: 2023-09-28 18:00:00
---
# Ruby 信件功能及本地瀏覽

## 開始

### 第一步、建立 mailer

使用指令建立 mailer 

```ruby=
$ bin/rails g mailer Order
```

指令建立的內容為

```ruby=
    create  app/mailers/order_mailer.rb
    invoke  erb
    create    app/views/order_mailer
 identical    app/views/layouts/mailer.text.erb
 identical    app/views/layouts/mailer.html.erb
    invoke  test_unit
    create    test/mailers/order_mailer_test.rb
    create    test/mailers/previews/order_mailer_preview.rb
```

### 第二步、設定預設信件寄件者

從 `application_mailer.rb` 裡 `from@example.com` 設定預設的寄件者

```ruby=
# app/mailers/application_mailer.rb

class ApplicationMailer < ActionMailer::Base
  default from: 'from@example.com'
  layout 'mailer'
end
```

### 第三步、新增 Mailer Action

可以在此設定 I18n 已方便未來做信件多語言

```ruby=
# app/mailers/order_mailer.rb

class OrderMailer < ApplicationMailer
  def notify(user)
    I18n.locale = user.lang
    @user = user
    I18n.with_locale do
      mail(
        :to => "<#{@user.email}>", 
        :subject => I18n.t('order_mailer.notify.subject')
      )
    end
  end
end
```

我目前設定已使用者的語言為主

### 第四步、建立信件內容

```ruby=
# app/views/order_mailer/notify.html.erb

<%= @user.name %>，你好：

感謝您的購買。
```

簡單的 html 範例

### 第五步、設置信件 Test

```ruby=
class OrderMailerPreview < ActionMailer::Preview
  def notify
    OrderMailer.notify(
    	user = User.first
    )
  end
end
```

設置完畢後即可使用此連結瀏覽信件

`http://localhost:3000/rails/mailers`

今天的分享就到此結束

### Thank you! :smile: