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
    identified_by :current_user

    def connect
      self.current_user = find_verified_user
    end

    private
      def find_verified_user
        if cu_user = User.find_by(id: cookies.signed[:user_id])
          cu_user
        else
          reject_unauthorized_connection
        end
      end
  end
end
```

action cable 中不能使用 session，我们可以用 cookie 来验证用户。下面在 SessionHelper 中加入 cookie，方便 action cable 使用

```ruby
module SessionsHelper
  def log_in(user)
    session[:user_address] = user.address
    user = User.find_by(address: session[:user_address])
    if user
      cookies.permanent.signed[:user_id] = user.id
    end
  end

  def current_user
    @current_user ||= User.find_by(address: session[:user_address])
  end

  def logged_in?
    !current_user.nil?
  end

  def log_out
    session.delete(:user_address)
    @current_user = nil
    cookies.delete(:user_id)
  end
end
```

### 订阅

```coffee
App.block = App.cable.subscriptions.create channel: "BlockChannel",
  connected: ->
    # Called when the subscription is ready for use on the server

  disconnected: ->
    # Called when the subscription has been terminated by the server

  received: (data) ->
    alert(data)
```

上述`channel: 'BlockChannel'`是必须的，声明像哪个频道订阅

### 处理订阅

```ruby
class BlockChannel < ApplicationCable::Channel
  def subscribed
    # 设置可以向哪些订阅者发布信息
    stream_from current_user.address
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
```

## 关于 action cable 的部署

### cable.yml

action cable 默认为“异步”适配器，当涉及多个进程时，它不起作用。因此，您需要配置 Action Cable 以使用其他适配器，例如 Redis 或 PostgreSQL。

```yml
production:
  adapter: redis
  url: redis://localhost:6379

staging:
  adapter: redis
  url: redis://localhost:6379

local: &local
  adapter: redis
  url: redis://localhost:6379

development: *local
test: *local
```

不要忘记启动 redis

### 在和 rails 相同的主机和端口使用 action cable

下面是 Rails 推荐的默认设置，也是最简单的设置.它的工作原理是将 ActionCable.server 挂载到 config / routes.rb 中的某个路径。这样，您的 Action Cable 服务器将在与您的应用程序相同的主机和端口上运行，但在子 URI 下运行。

在 router.rb 文件中

```ruby
mount ActionCable.server => '/cable'
```

你需要配置一个 `location`块 配置 cable 的请求,像下面这样：

```nginx
server {
    listen 80;
    server_name www.foo.com;
    root /path-to-your-app/public;
    passenger_enabled on;

    ### INSERT THIS!!! ###
    location /cable {
        passenger_force_max_concurrent_requests_per_process 0;
    }
}
```

为了应用的性能，必须添加`passenger_force_max_concurrent_requests_per_process 0`的配置，关于这个配置的详解请看 [文档](https://www.phusionpassenger.com/library/config/nginx/reference/#passenger_force_max_concurrent_requests_per_process)
