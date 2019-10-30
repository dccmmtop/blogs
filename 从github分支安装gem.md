---
tags: [gem,ruby]
date: 2018-09-27 10:12:16
---

默认从源 rubygems.org 安装 gem 包,下面演示一下如何从项目的某个分支安装：

### Gemfile

```ruby
gem 'rails', :git => "git://github.com/rails/rails.git", :ref => "4aded"
gem 'rails', :git => "git://github.com/rails/rails.git", :branch => "2-3-stable"
gem 'rails', :git => "git://github.com/rails/rails.git", :tag => "v2.3.5"
```

这种方式不需要再从本地构建 gem，需要指明源码位置，以及 ref,或者 branch 或者 tag

详情请看[ 官网 ](https://bundler.io/man/gemfile.5.html#GIT)

### 本地构建

- 克隆仓库

  ```shell
  $ git clone git://github.com/odorcicd/authlogic.git
  cd authlogic
  ```

- 切换分支

  `$ git checkout -b rails3 remotes/origin/rails3`

- 构建 gem

  `$ rake build gem`

- 安装 gem

  `$ gem install pkg/gemname-1.23.gem`

[stackflow](https://stackoverflow.com/questions/2823492/install-gem-from-github-branch)
