---
tags: [rails,CORS]
date: 2018-09-19 09:25:49
---

### 什么是跨域

理解跨域首先必须要了解同源策略。同源策略是浏览器上为安全性考虑实施的非常重要的安全策略。
那么什么是同源？我们知道，URL 由协议、域名、端口和路径组成，如果两个 URL 的协议、域名和端口相同，则表示他们同源。
我们用一个例子来说明：
URL: `http://www.example.com:8080/script/jquery.js`

在这个 url 中，各个字段分别代表的含义：

- http://——协议
- www——子域名
- example.com——主域名
- 8080——端口号
- script/jquery.js——请求的地址

  当协议、子域名、主域名、端口号中任意一各不相同时，都算不同的“域”。不同的域之间相互请求资源，就叫跨域。
  这里要注意，如果只是通过 AJAX 向另一个服务器发送请求而**不要求数据返回，是不受跨域限制的。浏览器只是限制不能访问另一个域的数据，即不能访问返回的数据，并不限制发送请求。**

  事实上，为了解决因同源策略而导致的跨域请求问题，解决方法有五种：

  1.  document.domain
  2.  Cross-Origin Resource Sharing(CORS)
  3.  Cross-document messaging
  4.  JSONP
  5.  WebSockets

### 什么是 CORS(跨域资源共享，Cross-Origin Resource Sharing)？

我们先来看看 wiki 上的定义：

> 跨来源资源共享（CORS）是一份浏览器技术的规范，提供了 Web 服务从不同网域传来沙盒脚本的方法，以避开浏览器的同源策略，是 JSONP 模式的现代版。与 JSONP 不同，CORS 除了 GET 要求方法以外也支持其他的 HTTP 要求。用 CORS 可以让网页设计师用一般的 XMLHttpRequest，这种方式的错误处理比 JSONP 要来的好。另一方面，JSONP 可以在不支持 CORS 的老旧浏览器上运作。现代的浏览器都支持 CORS。

由此我们可以知道， CORS 定义一种跨域访问的机制，可以让 AJAX 实现跨域访问。CORS 允许一个域上的网络应用向另一个域提交跨域 AJAX 请求。对于 CORS 来说，实现此功能非常简单，只需由服务器发送一个响应标头即可。服务器端对于 CORS 的支持，主要就是通过设置 Access-Control-Allow-Origin 来进行的。具体的关于 CORS 原理性的知识此处不再进行介绍，只在此对 CORS 和 JSONP 进行简单的比较.

CORS 与 JSONP 比较
CORS 与 JSONP 相比，更为先进、方便和可靠。

1.  JSONP 只能实现 GET 请求，而 CORS 支持所有类型的 HTTP 请求。
2.  使用 CORS，开发者可以使用普通的 XMLHttpRequest 发起请求和获得数据，比起 JSONP 有更好的错误处理。
3.  JSONP 主要被老的浏览器支持，它们往往不支持 CORS，而绝大多数现代浏览器都已经支持了 CORS。

### rack-cors 怎样解决跨域问题？

> Rack Middleware for handling Cross-Origin Resource Sharing (CORS), which makes cross-origin AJAX possible.

也就是说，这个 gem 是基于 CORS 来实现 Ajax 的跨域请求功能的，我们可以添加这个 gem 来解决我们项目中遇到的问题。
我们看到 gem 中给出的配置接口：

> Rack
> In config.ru, configure Rack::Cors by passing a block to the use command:

```ruby
use Rack::Cors do
  allow do
    origins 'localhost:3000', '127.0.0.1:3000',
      /\Ahttp:\/\/192\.168\.0\.\d{1,3}(:\d+)?\z/ # regular expressions can be used here
    resource '/file/list_all/', :headers => 'x-domain-token'
    resource '/file/at/_',
      :methods => [:get, :post, :delete, :put, :patch, :options, :head],
      :headers => 'x-domain-token',
      :expose => ['Some-Custom-Response-Header'],
      :max_age => 600 # headers to expose
  end
  allow do
    origins '_'
    resource '/public/\*', :headers => :any, :methods => :get
  end
end
```

Rails

Put something like the code below in config/application.rb of your Rails application. For example, this will allow GET, POST or OPTIONS requests from any origin on any resource.

```ruby
module YourApp
  class Application < Rails::Application # ... # Rails 3/4
    config.middleware.insert_before 0, "Rack::Cors" do
      allow do
        origins '_'
        resource '_', :headers => :any, :methods => [:get, :post, :options]
      end
    end # Rails 5
    config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins '_'
        resource '_', :headers => :any, :methods => [:get, :post, :options]
      end
    end
  end
end
```

可以看出，rack-cors 实际上直接给出了借口，我们在 bundle 这个 gem 后，直接在 `config/application.rb` 文件中添加配置信息即可，而无需自己在代码中添加有关跨域资源的策略信息。

### 实际应用

这个 gem 是用在被访问的资源服务器上的，用来定义哪些域可以访问资源以及可以访问自己的哪些资源等策略信息。这个 gem 可以很轻松很方便地解决 ajax 跨域问题。

- 安装 gem
  `gem 'rack-cors', :require => 'rack/cors'`
  修改 `config/application.rb`
  我们使用的是 rails，因此只需要做以下修改即可：

```ruby
config.middleware.insert*before 0, Rack::Cors do
  allow do
    origins '*'
    resource '\_', :headers => :any, :methods => [:get, :post, :options]
  end
end
```

其中，origins 用来配置可以请求自己资源的域，*表示任何域都可以请求；resource 用来配置自己的哪些资源可以被请求，*代表所有资源都可以被请求，methods 代表可以被请求的方法。

做完这两部，我们就可以实现跨域请求资源了。此时重启服务器，本地再次请求资源就会成功，同时我们可以看到自己的请求中多了类似下面的一些信息：

```
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Methods: GET, POST, OPTIONS
Access-Control-Max-Age: 1728000
Access-Control-Allow-Credentials: true
```

这样，我们便在 rails 中解决了 Ajax 的跨域请求资源的问题，项目也可以继续向前开发了。

```txt
 作者：vito1994
 链接：https://www.jianshu.com/p/c54a1dbaab24
 來源：简书
 简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
```
