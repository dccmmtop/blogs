---
tags: rails,cable
date: 2018-11-18 17:24:00
---

### 安装 redis

1.  `gem redis`
2.  bundle install

### 修改 cable.yml

```yml
development:
  adapter: redis
```

### 生成订阅

`rails g channel block speak`

### 连接设置

> 连接是客户端-服务器通信的基础。每当服务器接受一个 WebSocket，就会实例化一个连接对象。所有频道订阅（channel subscription）都是在继承连接对象的基础上创建的。连接本身并不处理身份验证和授权之外的任何应用逻辑。WebSocket 连接的客户端被称为连接用户（connection consumer）。每当用户新打开一个浏览器标签、窗口或设备，对应地都会新建一个用户-连接对（consumer-connection pair）。 连接是 ApplicationCable::Connection 类的实例。对连接的授权就是在这个类中完成的，对于能够识别的用户，才会继续建立连接。

例子：

```ruby
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    include SessionsHelper
    identified_by :current_user

    def connect
      logged_in?
    end
  end
end
```

### 订阅

```coffee
# App.block = App.cable.subscriptions.create {channel: "BlockChannel", address: "room_33"},
App.block = App.cable.subscriptions.create {channel: "BlockChannel", address: web3.eth.accounts[0]},
  connected: ->
    # Called when the subscription is ready for use on the server

  disconnected: ->
    # Called when the subscription has been terminated by the server

  received: (data) ->
    alert(data)
```

上述`channel: 'BlockChannel'`是必须的，声明像哪个频道订阅，`address: xxxx`是可选的，标明订阅者的身份，如果需要向指定的用户发布通知，需要设置这个参数。

### 处理订阅

```ruby
class BlockChannel < ApplicationCable::Channel
  def subscribed
    # 设置可以向哪些订阅者发布信息
    stream_from "#{params[:address]}"
    #stream_from "room_33"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end
```

### 测试

在 rails console 中

```ruby
# 向用户22发送一条通知
ActionCable.server.broadcast User.find(22).address, data: 22

# 向33号房间发送一条通知
#ActionCable.server.broadcast "room_33", data: 22
```
