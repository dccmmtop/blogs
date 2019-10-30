---
tags: [truffle]
date: 2018-08-08 11:02:14
---

### 使用方式

```
truffle [command] [options]
```

### 命令

#### build

构建一个开发中的 app 版本，创建`.build`目录。

```
truffle build
```

可选参数

- --dist: 创建一个可发布的 app 版本。仅在使用默认构造器时可用。

查看[6. 构建应用](http://truffle.tryblockchain.org/Truffle-build-%E9%BB%98%E8%AE%A4%E6%9E%84%E5%BB%BA%E5%99%A8.html)章节来了解更多。

#### console

运行一个控制台，里面包含已初始化，且随时可用的合约对象。

```
truffle console
```

一旦控制台启去吧，你可以使用通过命令行来使用你的合约，就像代码中那样。另外所有 Truffle 的列在这里的命令都可以在控制台使用。

可选参数：

- --network 名称：指定要使用的网络
- --verbose-rpc：输出 Truffle 与 RPC 通信的详细信息。

其它的[9. 控制台](http://truffle.tryblockchain.org/Truffle-UsingTheConsole-%E4%BD%BF%E7%94%A8%E6%8E%A7%E5%88%B6%E5%8F%B0.html)章节来了解更多。

#### compile

智能编译你的合约，仅会编译自上次编译后修改过的合约，除非另外指定强制刷新。

```
truffle compile
```

可选参数：

- --compile-all: 强制编译所有合约。
- --network 名称：指定使用的网络，保存编译的结果到指定的网络上。

#### create:contract

工具方法使用脚手架来创建一个新合约。名称需要符合驼峰命名：

```
$ truffle create:contract MyContract
```

#### create:test

工具方法，使用脚手架来创建一个新的测试方法。名称需要符合驼峰命名。

```
$ truffle create:test MyTest
```

#### migrate

运行工程的移植。详情见`移植`相关的章节。

```
truffle migrate
```

可选的参数：

- --reset: 从头运行所有的移植。
- --network 名称：指定要使用的网络，并将编译后的资料保存到那个网络。
- --to number：将版本从当前版本移植到序号指定的版本。
- --compile-all: 强制编译所有的合约
- --verbose-rpc：打印 Truffle 与 RPC 交互的详细日志。

#### exec

在 Truffle 的环境下执行一个 Javascript 文件。环境内包含，web3，基于网络设置的默认 provider，作为全局对象的你的合约对象。这个 Javascript 文件需要 export 一个函数，这样 Truffle 才可以执行。查看[10. 外部脚本](http://truffle.tryblockchain.org/Truffle-WrtingExternalScripts-%E5%A4%96%E9%83%A8%E8%84%9A%E6%9C%AC.html)来了解更多。

```
$ truffle exec /path/to/my/script.js
```

可选参数：

- --network 名称：名称：指定要使用的网络，并将编译后的资料保存到那个网络。

#### init

在当前目录下初始化一个全新的 APP，一个全新的工程。会自带默认合约和前端配置。

```
$ truffle init
```

#### list

列出所有可用的命令，与`--help`等同。

```
truffle list
```

#### serve

在`http://localhost:8080`提供编译的 app 对应的服务，且在需要的时候自动构建，自动部署。与`truffle watch`类似，区别在于这里增加 web 服务器功能。

```
truffle serve
```

可选参数：

- -p port: 指定 http 服务的端口。默认是 8080。
- --network 名称：名称：指定要使用的网络，并将编译后的资料保存到那个网络。

#### test

运行所有在`./test`目录下的测试用例。或可选的运行单个测试文件。

```
$ truffle test [/path/to/test/file]
```

可选参数：

- --network 名称：指定要使用的网络，并将编译后的资料保存到那个网络。
- --compile-all: 强制编译所有的合约
- --verbose-rpc：打印 Truffle 与 RPC 交互的详细日志。

#### version

输出版本号然后退出。

```
truffle version
```

#### watch

Watch 合约，APP，和配置文件变化，在需要时自动构建 APP。

```
truffle watch
```

如果任何问题，欢迎留言批评指正。
