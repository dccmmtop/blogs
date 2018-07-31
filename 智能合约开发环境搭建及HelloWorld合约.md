---
tags: solidity,geth,contract
---

如果你对于以太坊智能合约开发还没有概念（本文会假设你已经知道这些概念），建议先阅读[入门篇](https://learnblockchain.cn/2017/11/20/whatiseth/)。
就先学习任何编程语言一样，入门的第一个程序都是 Hello World。今天我们来一步一步从搭建以太坊智能合约开发环境开始，讲解智能合约的 Hello World 如何编写。

## 开发环境搭建

### Solidity 安装

强烈建议新手使用[Remix -Solidity IDE](http://remix.ethereum.org/)来进行开发。
Remix 是一个基于浏览器的 Solidity，就可以不用安装 Solidity，本文的 Hello World 教程也将基于 Remix Solidity IDE 来进行。

如果你想自己安装请参考[Solidity 安装指引](https://solidity.readthedocs.io/en/develop/installing-solidity.html)。

> 更新，开发环境搭建还可以看另一篇文章: [搭建智能合约开发环境 Remix IDE 及使用](https://learnblockchain.cn/2018/06/07/remix-ide/)。

### geth 安装

Mac 下安装命令如下：其他平台参考：[geth 官方安装指引](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum)

```
brew tap ethereum/ethereum
brew install ethereum
```

> brew 是 Mac 下的包管理工具，和 Ubuntu 里的 apt-get 类似

安装完以后，就是把 geth 控制台启动。

## 启动环境

在入门篇讲过，geth 是一个以太坊客户端，现在利用 geth 启动一个以太坊（开发者）网络节点。

```
geth --datadir testNet --dev console 2>> test.log
```

执行命名后，会进入 geth 控制台，这时光标停在一个向右的箭头处，像这样：
[![img](https://learnblockchain.cn/images/open_geth_eth.jpg)](https://learnblockchain.cn/images/open_geth_eth.jpg)

命令参数说明（更多命令详解可阅读[Geth 命令用法-参数详解篇](https://learnblockchain.cn/2017/11/29/geth_cmd_options/)）:
**–dev** 启用开发者网络（模式），开发者网络会使用 POA 共识，默认预分配一个开发者账户并且会自动开启挖矿。
**–datadir** 后面的参数是区块数据及秘钥存放目录。
第一次输入命令后，它会放在当前目录下新建一个 testNet 目录来存放数据。
**console** 进入控制台
**2>> test.log** 表示把控制台日志输出到 test.log 文件

为了更好的理解，建议新开一个命令行终端，实时显示日志：

```
tail -f test.log
```

## 准备账户

部署智能合约需要一个外部账户，我们先来看看分配的开发者账户，在控制台使用以下命令查看账户：

```
> eth.accounts
```

回车后，返回一个账户数组，里面有一个默认账户，如：
[![img](https://learnblockchain.cn/images/geth_accounts_1.jpg)](https://learnblockchain.cn/images/geth_accounts_1.jpg)

> 也可以使用 personal.listAccounts 查看账户，

再来看一下账户里的余额，使用一下命令：

```
> eth.getBalance(eth.accounts[0])
```

**eth.accounts[0]**表示账户列表第一个账户
回车后，可以看到大量的余额，如：
1.15792089237316195423570985008687907853269… e+77

开发者账户因余额太多，如果用这个账户来部署合约时会无法看到余额变化，为了更好的体验完整的过程，这里选择创建一个新的账户。

### 创建账户

使用以下命令创建账户：

```
> personal.newAccount("TinyXiong")
```

TinyXiong 为新账户的密码，回车后，返回一个新账户。

这时我们查看账户列表：

```
> eth.accounts
```

可以看到账户数组你包含两个账户，新账户在第二个（索引为 1）位置。

现在看看账户的余额：

```
> eth.getBalance(eth.accounts[1])
0
```

回车后，返回的是 0，新账户是 0。结果如：
[![img](https://learnblockchain.cn/images/geth_accounts_2.jpg)](https://learnblockchain.cn/images/geth_accounts_2.jpg)

### 给新账户转账

我们知道没有余额的账户是没法部署合约的，那我们就从默认账户转 1 以太币给新账户，使用以下命令（请使用你自己 eth.accounts 对应输出的账户）：

```
eth.sendTransaction({from: '0xb0ebe17ef0e96b5c525709c0a1ede347c66bd391', to: '0xf280facfd60d61f6fd3f88c9dee4fb90d0e11dfc', value: web3.toWei(1, "ether")})
```

> 在打开的**tail -f test.log**日志终端里，可以同时看到挖矿记录
> 再次查看新账户余额，可以新账户有 1 个以太币
> [![img](https://learnblockchain.cn/images/geth_accounts_3.jpg)](https://learnblockchain.cn/images/geth_accounts_3.jpg)

### 解锁账户

在部署合约前需要先解锁账户（就像银行转账要输入密码一样），使用以下命令：

```
personal.unlockAccount(eth.accounts[1],"TinyXiong");
```

“TinyXiong” 是之前创建账户时的密码
解锁成功后，账户就准备完毕啦，接下来就是编写合约代码。

## 编写合约代码

现在我们来开始编写第一个智能合约代码，solidity 代码如下：

```
pragma solidity ^0.4.18;
contract hello {
    string greeting;

    function hello(string _greeting) public {
        greeting = _greeting;
    }

    function say() constant public returns (string) {
        return greeting;
    }
}
```

简单解释下，我们定义了一个名为 hello 的合约，在合约初始化时保存了一个字符串（我们会传入 hello world），每次调用 say 返回字符串。
把这段代码写(拷贝)到[Browser-Solidity](https://ethereum.github.io/browser-solidity)，如果没有错误，点击 Details 获取部署代码，如：
[![img](https://learnblockchain.cn/images/eth_code_hello_step1.jpeg)](https://learnblockchain.cn/images/eth_code_hello_step1.jpeg)

在弹出的对话框中找到 WEB3DEPLOY 部分，点拷贝，粘贴到编辑器后，修改初始化字符串为 hello world。
[![img](https://learnblockchain.cn/images/eth_code_hello_step2.jpeg)](https://learnblockchain.cn/images/eth_code_hello_step2.jpeg)

> solidity 在博文写作时（2017/11/24），版本为 0.4.18，solidity 发展非常快，solidity 版本之间有可能不能兼容，这是你可以在 Browser-Solidity 的 Settings 里选择对应的编译器版本。
> Browser-Solidity 也不停的更新中，截图可能和你看到的界面不一样。

## 部署合约

Browser-Solidity 生成的代码，拷贝到编辑器里修改后的代码如下：

```js
var _greeting = 'Hello World';
var helloContract = web3.eth.contract([
  {
    constant: true,
    inputs: [],
    name: 'say',
    outputs: [{name: '', type: 'string'}],
    payable: false,
    stateMutability: 'view',
    type: 'function',
  },
  {
    inputs: [{name: '_greeting', type: 'string'}],
    payable: false,
    stateMutability: 'nonpayable',
    type: 'constructor',
  },
]);
var hello = helloContract.new(
  _greeting,
  {
    from: web3.eth.accounts[1],
    data:
      '0x6060604052341561000f57600080fd5b6040516102b83803806102b8833981016040528080518201919050508060009080519060200190610041929190610048565b50506100ed565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f1061008957805160ff19168380011785556100b7565b828001600101855582156100b7579182015b828111156100b657825182559160200191906001019061009b565b5b5090506100c491906100c8565b5090565b6100ea91905b808211156100e65760008160009055506001016100ce565b5090565b90565b6101bc806100fc6000396000f300606060405260043610610041576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff168063954ab4b214610046575b600080fd5b341561005157600080fd5b6100596100d4565b6040518080602001828103825283818151815260200191508051906020019080838360005b8381101561009957808201518184015260208101905061007e565b50505050905090810190601f1680156100c65780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b6100dc61017c565b60008054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156101725780601f1061014757610100808354040283529160200191610172565b820191906000526020600020905b81548152906001019060200180831161015557829003601f168201915b5050505050905090565b6020604051908101604052806000815250905600a165627a7a723058204a5577bb3ad30e02f7a3bdd90eedcc682700d67fc8ed6604d38bb739c0655df90029',
    gas: '4700000',
  },
  function(e, contract) {
    console.log(e, contract);
    if (typeof contract.address !== 'undefined') {
      console.log(
        'Contract mined! address: ' +
          contract.address +
          ' transactionHash: ' +
          contract.transactionHash,
      );
    }
  },
);
```

第 1 行：修改字符串为 Hello World
第 2 行：修改合约变量名
第 3 行：修改合约实例变量名，之后可以直接用实例调用函数。
第 6 行：修改部署账户为新账户索引，即使用新账户来部署合约。
第 8 行：准备付的 gas 费用，IDE 已经帮我们预估好了。
第 9 行：设置部署回调函数。

拷贝回 geth 控制台里，回车后，看到输出如：

```
Contract mined! address: 0x79544078dcd9d560ec3f6eff0af42a9fc84c7d19 transactionHash: 0xe2caab22102e93434888a0b8013a7ae7e804b132e4a8bfd2318356f6cf0480b3
```

说明合约已经部署成功。

> 在打开的**tail -f test.log**日志终端里，可以同时看到挖矿记录

现在我们查看下新账户的余额：

```
> eth.getBalance(eth.accounts[1])
```

是不是比之前转账的余额少呀！

## 运行合约

```js
> hello.say()
"Hello World"
```

输出 Hello World，我们第一个合约 Hello World，成功运行了。

运行截图如下：
[![img](https://learnblockchain.cn/images/init_example_show.jpg)](https://learnblockchain.cn/images/init_example_show.jpg)

本文会随 geth，solidity 语言版本升级保持更新，查看本文原始链接：<https://learnblockchain.cn/2017/11/24/init-env/>

第一个合约的意义更重要的是体验智能合约开发流程，对于初学者一些可以选择先放弃一些细节，开发流程打通之后，可以增强信心进行下一步的学习。
有问题就加入[深入浅出区块链群](https://t.xiaomiquan.com/RfAu7uj)一起来交流吧。

- **本文作者：** Tiny 熊
- **本文链接：** <https://learnblockchain.cn/2017/11/24/init-env/>
- **版权声明：** 本文采用 [CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.zh) 许可协议。转载请注明出处！
