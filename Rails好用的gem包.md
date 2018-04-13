## Rails 好用的gem包

* 用户登录、权限:
```text
devise - plataformatec/devise · GitHub

rolify - RolifyCommunity/rolify · GitHub

pundit - elabs/pundit · GitHub
```
* 状态机
```text
statesman - gocardless/statesman · GitHub
```
* 分页
``` text
kaminari - amatsuda/kaminari · GitHub
```
* 图表
``` text
chartkick - ankane/chartkick · GitHub
```
* 表单
``` text
simple_form - plataformatec/simple_form · GitHub
```
* 部署
``` text
mina - mina-deploy/mina · GitHub
```
* SEO
``` text
meta-tags - kpumuk/meta-tags · GitHub
```
* 配置

```reStructuredText
settingslogic - binarylogic/settingslogic · GitHub
```
* 队列
```text
sidekiq - mperham/sidekiq · GitHub
```
* 监控
```text
skylight - skylightio/skylight-ruby · GitHub
```
* 错误处理
```text
better_errors - charliesome/better_errors · GitHub
```
* 代码检查
```text
rubocop - bbatsov/rubocop · GitHub
```
* 日志
```text
lograge - roidrage/lograge · GitHub
```
* 树形结构
```text
closure_tree - mceachen/closure_tree · GitHub
ElasticSearch
searchkick - ankane/searchkick · GitHub
```
* 统计分析
```text
mixpanel-ruby - mixpanel/mixpanel-ruby · GitHub
meta_events - swiftype/meta_events · GitHub
```
* 浏览器检测
```text
browser - fnando/browser · GitHub
```
* 假数据
```text
fabrication - paulelliott/fabrication · GitHub
```
* 测试相关
```text
capybara - jnicklas/capybara · GitHub
mocha - freerange/mocha · GitHub
poltergeist - teampoltergeist/poltergeist · GitHub
database_cleaner - DatabaseCleaner/database_cleaner · GitHub
timecop - travisjeffery/timecop · GitHub
cucumber - cucumber/cucumber · GitHub
```

* bootstrap（bootstrap-sass）
  > 这个不用解释了吧，对于没有专业前端小伙伴的项目和团队是福音。而且对于想要学习前端技术的同学，bootstrap的源码非常值得进行深入的学习和理解。当然，缺点也是有的，就是大家的网站做出来都比较像，建议有条件的小伙伴使用的时候还是多进行一些个性化修改。

* Capistrano（capistrano | RubyGems.org）

  > Capistrano最初就是用来向服务器部署ruby应用的，当然现在也开始支持其他类型项目的部署。Capistrano是一种在多台服务器上运行脚本的开源工具，它主要用于部署web应用。它自动完成多台服务器上新版本的同步更新，包括数据库的改变。使用起来非常方便，基本也是Rails应用开发必备。

  ​

* bcrypt（bcrypt | RubyGems.org）

  > bcrypt是一个跨平台的文件加密工具。由它加密的文件可在所有支持的操作系统和处理器上进行转移。它的口令必须是8至56个字符，并将在内部被转化为448位的密钥。在rails上使用起来相当简便，web开发一般是少不了的。

* sprockets（sprockets | RubyGems.org）

  > Sprockets 是一个 Ruby 库，用来检查 JavaScript 文件的相互依赖关系，用以优化网页中引入的js文件，以避免加载不必要的js文件，加快网页访问速度。这个现在貌似是rails工程默认自带gem，记不太清了，足见重要性。但是我在使用中发现有时候会跟bootstrap的js库发生冲突，主要是版本问题，有使用的小伙伴需要注意一下。

* Paperclip（paperclip | RubyGems.org）

  > paperclip基本上已经是rails的御用图片上传gem了，功能强大使用方便，但是唯一不太好的就是图片剪裁上面功能比较有限。paperclip依赖于ImageMagic，大部分linux包管理器（如apt yum portage等）中应该都能找到这个软件包。

* rmagick（rmagick | RubyGems.org）

  > 这个gem弥补了paperclip在图片剪裁上的不足。功能十分十分十分的强大，文档也很齐全，只不过是全英文，中文资料比较少。如果项目有要开发头像剪裁上传，那么用rmagic剪裁，paperclip上传是比较好的解决方案。


* friendly_id（friendly_id | RubyGems.org）

  > 现在大部分网站都已经是友好的url地址了，如果你的rails项目还用数字作为id进行查询那就太low了。

* will_paginate（will_paginate）

  > 老版本的will_paginate分业是基于plugin方式的，新版本的will_paginate已经抛弃了这样的做法，转而使用gem的方式。这样一来安装和使用更加方便，配合一些前端的分页加载库，会让分页加载功能开发非常快捷。

* ransack（ransack | RubyGems.org）

  > 搜索功能一般分为分词和不分词的，如果你只想做用户和文章标题检索之类的功能，那么不分词的搜索gem比较合适，类似的gem挺多的，我常用的是这个。

* sunspot（sunspot | RubyGems.org）

  > 接上面，全文搜索的gem也不少，sunspot对于小型项目的话，稍微有些重。如果没有那么多要求的话，sphinx也可以考虑，速度快，占用资源低。

* Geocoder（geocoder | RubyGems.org）

  > 说实话在web上做定位确实比较乏力，尽量还是用手机客户端吧

* puma（puma/puma · GitHub）

  > 对于想要处理并发请求的web项目，Puma 是和 Unicorn相竞争的 Web 服务器，它能够处理并发请求。Puma 使用线程，以及工作者进程，能够更多的利用可用的 CPU。在 Puma 中，如果整个基础代码是线程安全的，那么你可用利用线程。否则，在使用 Puma 的时候，你只能使用工作者进程进行拓展。Puma的设置也比较简单，官方有详细的使用文档，RubyChina也使用Puma，个人推荐。

* Emoji（wpeterson/emoji · GitHub）

  > 现在只要涉及社交的项目估计很少有不用表情的了，Emoji是个集成度比较高的Emoji表情转码库，使用方便，当然大部分情况下还是要配合前端使用。