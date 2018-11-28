---
tags: rails,email
date: 2018-11-28 17:26:31
---

rails 中使用邮件服务是非常方便的，直接加配置文件就可以，参考[指南](https://ruby-china.github.io/rails-guides/action_mailer_basics.html)

qq 邮箱正确的配置如下

production.rb / development.rb

```ruby
ActionMailer::Base.delivery_method = :smtp
config.action_mailer.perform_deliveries = true
config.action_mailer.raise_delivery_errors = true
config.action_mailer.default :charset => "utf-8"
ActionMailer::Base.smtp_settings = {
  :address              => 'smtp.qq.com',
  :port                 => 465,
  :domain               => 'qq.com',
  :user_name            => ENV['qq_mail_address']
  # 授权码
  :password             =>  ENV['qq_mail_address']
  :authentication       => 'plain',
  :ssl => true,
  :enable_starttls_auto => true
}
```

切记，qq 邮箱后台要开启 POP3/SMTP 服务，开启的时候需要通过发送短信息启用，启用的时候会生成一个授权码，配置文件的 password 只需要填写授权码即可。
