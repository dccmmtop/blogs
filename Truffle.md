---
tags: [truffle,contract]
date: 2018-09-01 12:00:00
---

## 安装 Truffle 4.0

```shell
npm install -g truffle@4.0.0
```

查看安装的版本：

```shell
truffle version
Truffle v4.0.0 (core: 4.0.0)
Solidity v0.4.18 (solc-js)
```

## 安装 TestRPC

```shell
npm install -g ethereumjs-testrpc
/usr/local/bin/testrpc -> /usr/local/lib/node_modules/ethereumjs-testrpc/build/cli.node.js
+ ethereumjs-testrpc@6.0.1
added 1 package and updated 2 packages in 10.648s
```
<!--more-->
## 初始化一个 Truffle 项目

```shell
mkdir test_truffle
cd test_truffle
truffle init

Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
```

完成后，你将拥有如下目录：

- `contracts` 智能合约目录

- `migrations` 发布脚本目录

- `test` 存放测试文件

- `truffle.js` Truffle 的配置文件

## 编译合约

要编译合约，使用`truffle compile`命令，可以将原始代码编译成以太坊认可的字节码

`Truffle`仅默认编译自上次编译后被修改过的文件，来减少不必要的编译。如果你想编译全部文件，可以使用`--compile-all`选项

```
truffle compile --compile-all
```

`Truffle`需要定义的合约名称和文件名准确匹配,这种匹配是区分大小写的，也就是说大小写也要一致。推荐大写每一个开头字母。

文件之间的相互依赖，可以使用`import`进行合约间的引用，Truffle 将会按正确顺序依次编译合约，并在需要的时候自动关联库。例如：

```
import "./AnotherContract.sol";
```

# 部署智能合约

编辑`truffle.js`配置文件，设置我们稍后要部署智能合约的位置，内容如下：

```js
module.exports = {
    networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*"
        }
    }
```

接下来，我们会使用上面的智能合约，分别在`testRPC`和`geth`中进行部署测试。

`truffle`的智能合约项目部署，使用下面的命令：

```shell
truffle migrate
```

> 这个命令会执行所有`migrations`目录下的 js 文件。如果之前执行过`truffle migrate`命令，再次执行，只会部署新的 js 文件，如果没有新的 js 文件，不会起任何作用。如果使用`--reset`参数，则会重新的执行所有脚本的部署。

如果要部署到指定的网络，可以使用`--network`参数，例如：

```shell
truffle migrate --network live
```

多个网络的配置格式如下：

```js
networks: {
  development: {
    host: "localhost",
    port: 8545,
    network_id: "*" // match any network
  },
  live: {
    host: "178.25.19.88", // Random IP for example purposes (do not use)
    port: 80,
    network_id: 1,        // Ethereum public network
    // optional config values:
    // gas  Gas limit used for deploys. Default is 4712388
    // gasPrice Gas price used for deploys. Default is 100000000000 (100 Shannon).
    // from - default address to use for any transaction Truffle makes during migrations
    // provider - web3 provider instance Truffle should use to talk to the Ethereum network.
    //          - if specified, host and port are ignored.
  }
}
```

### 通过 truffle migrate 命令，对合约进行部署

使用`truffle migrate`命令，发布项目：

```
➜ /Users/lion/my_project/_eth/test_truffle >truffle migrate
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xd9e5fa242d29362e57e3da7b0bf6f71b72767972fc15240ed3a02d341e814a44
  Migrations: 0x4bedd1bb517ff9a54f6f3df8eba821ff16a4109b
  Deploying Hello_mshk_top...
  ... 0xc22ff050e189c2561d40250077ee4bf628f957899dfa8d0b5fa50e3ab7a896b0
  Hello_mshk_top: 0xa7ef5037ff81d7932e01b68a503ca4587f00b35c
Saving successful migration to network...
  ... 0x49505a54042f5f74146fbfafef63dd408cb0a7a0c66214ebbaa3217e443d792a
Saving artifacts...
```
