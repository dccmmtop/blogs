---
tags: [bundle]
date: 2018-09-27 10:40:42
---

### bundle install
`bundle install`: 安装Gemfile中指定的依赖项

```shell
bundle install [--binstubs[=DIRECTORY]]
                 [--clean]
                 [--deployment]
                 [--force]
                 [--frozen]
                 [--full-index]
                 [--gemfile=GEMFILE]
                 [--jobs=NUMBER]
                 [--local]
                 [--no-cache]
                 [--no-prune]
                 [--path PATH]
                 [--quiet]
                 [--retry=NUMBER]
                 [--shebang]
                 [--standalone[=GROUP[ GROUP...]]]
                 [--system]
                 [--trust-policy=POLICY]
                 [--with=GROUP[ GROUP...]]
                 [--without=GROUP[ GROUP...]]
```

### 描述

安装Gemfile中指定的gem包，如果第一次运行 bundle install(没有Gemfile.lock),Bundler将会从远程获取源码，解决依赖，安装所有需要的包。

如果Gemlfile.lock存在，并且Gemfile没有更新，Bundler会使用Gemfile.lock中的依赖项获取源码，而不是根据Gemfile解决依赖关系。

如果Gemfile.lock存在，并且您已更新Gemfile,Bundler将使用Gemfile.lock中的依赖项来处理您未更新的所有gem，但会重新解析您更新的gem的依赖项。您可以在CONSERVATIVE UPDATING下找到有关此更新过程的更多信息。

### 选项

你可以在每次运行`bundle install`时带上下面的任意参数：
`--binstubs`, `--deployment`, `--path`, `--without`

* `--binstubs[=<directory>]`

创建一个目录（默认是~/bin）存放gem包中的一些可执行文件。这些可执行文件在Bundler的上下文中运行，如果使用，你可以将此目录添加到环境变量中，例如，如果`rails` gem附带了rails可执行文件，则此标志将创建一个bin / rails可执行文件，以确保使用捆绑的gem来解析所有引用的依赖项。

* `--clean`

完成安装后，Bundler将删除当前Gemfile中不存在的gem。别担心，目前使用的宝石不会被删除。

* `--deployment`

在部署模式下，Bundler将“推出”捆绑包以进行生产或CI使用。请仔细检查是否要在开发环境中启用此选项。

* `--force`

强制下载每个gem，即使所需的版本已在本地可用。

* `--frozen`

安装后，不允许更新Gemfile.lock

* `--gemfile=<gemfile>`

指定gemfile的位置，默认是根目录下的Gemfile

* `--jobs=[<numer>]` `-j[<number>]`

并行下载和安装作业的最大数量。默认值为1

* `--local`

不要尝试连接到rubygems.org。相反，Bundler将使用已存在于Rubygems缓存或`vender/cache`中的gem。

* `--quiet`

静默安装，退出状态保存在`$?`

* `--retry=[<number>]`

网络失败时重试，也可以指定重试的次数

* `--shebang=<ruby-executable>`

使用指定的ruby可执行文件去执行指定的脚本（--binstubs创造的），如果`--shebang jruby`和`--binstubs`一起使用，那么将会使用`jruby`执行。

