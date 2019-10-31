---
tags: [contract,solidity]
---

### 本文属于转载内容，[转载来源](http://me.tryblockchain.org/blockchain-solidity-event.html)

在介绍事件前，我们先明确事件，日志这两个概念。事件发生后被记录到区块链上成为了日志。总的来说，事件强调功能，一种行为；日志强调存储，内容。

事件是以太坊 EVM 提供的一种日志基础设施。事件可以用来做操作记录，存储为日志。也可以用来实现一些交互功能，比如通知 UI，返回函数调用结果等[1](http://me.tryblockchain.org/blockchain-solidity-event.html#fn1)。

## 事件

当定义的事件触发时，我们可以将事件存储到 EVM 的交易日志中，日志是区块链中的一种特殊数据结构。日志与合约关联，与合约的存储合并存入区块链中。只要某个区块可以访问，其相关的日志就可以访问。但在合约中，我们不能直接访问日志和事件数据（即便是创建日志的合约）。下面我们来看看，如何在 Solidity 中实现一个事件：
<!--more-->

```js
pragma solidity ^0.4.0;

contract Transfer{
  event transfer(address indexed _from, address indexed _to, uint indexed value);

  function deposit() payable {
    address current = this;
    uint value = msg.value;
    transfer(msg.sender, current, value);
  }


  function getBanlance() constant returns(uint) {
      return this.balance;
  }

  /* fallback function */
  function(){}
}
```

从上面的例子中，我们使用`event`关键字定义一个事件，参数列表中为要记录的日志参数的名称及类型。

## 监听事件

在 web3.js 中，提供了响应事件的方法，如下[2](http://me.tryblockchain.org/blockchain-solidity-event.html#fn2)：

```js
var event = myContract.transfer();

// 监听
event.watch(function(error, result) {
  console.log('Event are as following:-------');

  for (let key in result) {
    console.log(key + ' : ' + result[key]);
  }

  console.log('Event ending-------');
});
```

另外一种简便写法是直接加入事件回调，这样就不用再写`watch`的部分：

```js
var event = myContract.transfer(function(error, result) {
  console.log('Event are as following:-------');

  for (let key in result) {
    console.log(key + ' : ' + result[key]);
  }

  console.log('Event ending-------');
});
```

注意：在操作执行完成后，我们要记得调用`event.stopWatching();`来终止监听。

## 检索日志

### Indexed 属性

可以在事件参数上增加`indexed`属性，最多可以对三个参数增加这样的属性。加上这个属性，可以允许你在 web3.js 中通过对加了这个属性的参数进行值过滤，方式如下[2](http://me.tryblockchain.org/blockchain-solidity-event.html#fn2)：

```js
var event = myContract.transfer({value: '100'});
```

上面实现的是对`value`值为`100`的日志，过滤后的返回。

如果你想同时匹配多个值，还可以传入一个要匹配的数组。

```js
var event = myContract.transfer({value: ['99', '100', '101']});
```

增加了`indexed`的参数值会存到日志结构的`Topic`部分，便于快速查找。而未加`indexed`的参数值会存在`data`部分，成为原始日志。需要注意的是，如果增加`indexed`属性的是数组类型（包括`string`和`bytes`），那么只会在`Topic`存储对应的数据的`web3.sha3`哈希值，将不会再存原始数据。因为 Topic 是用于快速查找的，不能存任意长度的数据，所以通过 Topic 实际存的是数组这种非固定长度数据哈希结果。要查找时，是将要查找内容哈希后与 Topic 内容进行匹配，但我们不能反推哈希结果，从而得不到原始值[3](http://me.tryblockchain.org/blockchain-solidity-event.html#fn3)。

### 其它过滤参数

事件还支持传入其它参数对象来限定可检索的范围，支持`fromBlock`，`toBlock`等过滤条件，详见[https://github.com/ethereum/wiki/wiki/JavaScript-API#contract-events，链接的第二个参数[^filter\]。](https://github.com/ethereum/wiki/wiki/JavaScript-API#contract-events%EF%BC%8C%E9%93%BE%E6%8E%A5%E7%9A%84%E7%AC%AC%E4%BA%8C%E4%B8%AA%E5%8F%82%E6%95%B0%5B%5Efilter%5D%E3%80%82)

```js
var event = myContract.transfer(
  {value: '100'},
  {fromBlock: 0, toBlock: 'latest'},
);
```

上面的代码实现了从第一块开始搜索日志。

## Topic

日志中存储的不同的索引事件就叫不同的主题。比如，事件定义，`event transfer(address indexed _from, address indexed _to, uint value)`有三个主题，第一个主题为默认主题，即事件签名`transfer(address,address,uint256)`，但如果是声明为`anonymous`的事件，则没有这个主题；另外两个`indexed`的参数也会分别形成两个主题，可以分别通过`_from`，`_to`主题来进行过滤。如果数组，包括字符串，字节数据做为索引参数，实际主题是对应值的 Keccak-256 哈希值。

```js
var defaultTopic = web3.sha3('transfer(address,address,uint256)1');
var event = myContract.transfer(
  {value: '100'},
  {fromBlock: 0, toBlock: 'latest', topics: [defaultTopic]},
);
```

备注：Topic 过滤似乎没有生效，待研究

## 其它

事件可以被继承。

日志的 Merkle 证明是可能的，所以在区块链外，如果能提供这样一种证明，可以证明日志确实存在于区块链上。

## 底层的日志接口

可以通过底层的日志接口来访问底层的日志机制。通过函数`log0`，`log1`，`log2`，`log3`，`log4`。`logi`支持`i + 1`个类型为`bytes32`的参数。其中第一个参数是日志的`data`部分，其它参数为`topic`。所以下述事件：

```js
event Deposit(
        address indexed _from,
        bytes32 indexed _id,
        uint _value
    );
```

使用 API 的等同写法为:

```js
log3(
  msg.value,
  0x50cb9fe53daa9737b786ab3646f04d0150dc50ef4e75f59509d83667ad5adb20,
  msg.sender,
  _id,
);
```

第一个参数为非`indexed`的`data`部分值。第二个参数为默认主题，即事件签名的哈希值`keccak256("Deposit(address,hash256,uint256)")`，后面两个是按顺序的`indexed`的主题。

## 使用 web3.js 读取事件的完整例子

下面是一个使用以太坊提供的工具包 web3.js 访问事件的完整例子：

```js
let Web3 = require('web3');
let web3;

if (typeof web3 !== 'undefined') {
  web3 = new Web3(web3.currentProvider);
} else {
  // set the provider you want from Web3.providers
  web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));
}

let from = web3.eth.accounts[0];

//编译合约
let source =
  'pragma solidity ^0.4.0;contract Transfer{  event transfer(address indexed _from, address indexed _to, uint indexed value);  function deposit() payable {    address current = this;    uint value = msg.value;    transfer(msg.sender, current, value);  }  function getBanlance() constant returns(uint) {      return this.balance;  }  /* fallback function */  function(){}}';
let transferCompiled = web3.eth.compile.solidity(source);

console.log(transferCompiled);
console.log('ABI definition:');
console.log(transferCompiled['info']['abiDefinition']);

//得到合约对象
let abiDefinition = transferCompiled['info']['abiDefinition'];
let transferContract = web3.eth.contract(abiDefinition);

//2. 部署合约

//2.1 获取合约的代码，部署时传递的就是合约编译后的二进制码
let deployCode = transferCompiled['code'];

//2.2 部署者的地址，当前取默认账户的第一个地址。
let deployeAddr = web3.eth.accounts[0];

//2.3 异步方式，部署合约
//警告，你不应该每次都部署合约，这里只是为了提供一个可以完全跑通的例子！！！
transferContract.new(
  {
    data: deployCode,
    from: deployeAddr,
    gas: 1000000,
  },
  function(err, myContract) {
    if (!err) {
      // 注意：这个回调会触发两次
      //一次是合约的交易哈希属性完成
      //另一次是在某个地址上完成部署

      // 通过判断是否有地址，来确认是第一次调用，还是第二次调用。
      if (!myContract.address) {
        console.log(
          'contract deploy transaction hash: ' + myContract.transactionHash,
        ); //部署合约的交易哈希值

        // 合约发布成功后，才能调用后续的方法
      } else {
        console.log('contract deploy address: ' + myContract.address); // 合约的部署地址

        console.log('Current balance: ' + myContract.getBanlance());

        var event = myContract.transfer();

        // 监听
        event.watch(function(error, result) {
          console.log('Event are as following:-------');

          for (let key in result) {
            console.log(key + ' : ' + result[key]);
          }

          console.log('Event ending-------');
        });

        //使用transaction方式调用，写入到区块链上
        myContract.deposit.sendTransaction(
          {
            from: deployeAddr,
            value: 100,
            gas: 1000000,
          },
          function(err, result) {
            console.log('Deposit status: ' + err + ' result: ' + result);
            console.log('After deposit balance: ' + myContract.getBanlance());

            //终止监听，注意这里要在回调里面，因为是异步执行的。
            event.stopWatching();
          },
        );
      }
    }
  },
);
```

#### 关于作者

> 专注基于以太坊（Ethereum）的相关区块链（Blockchain）技术，了解以太坊，Solidity，Truffle，web3.js。
>
> 个人博客: [http://tryblockchain.org](http://tryblockchain.org/)
> 版权所有，转载注明出处

#### 参考资料

---

1.  事件、日志的技术指引。<http://me.tryblockchain.org/Blockchain-event-bestpractice.html> [↩](http://me.tryblockchain.org/blockchain-solidity-event.html#fnref1)
2.  web3.js 提供的事件操作 API。 <https://github.com/ethereum/wiki/wiki/JavaScript-API#contract-events> [↩](http://me.tryblockchain.org/blockchain-solidity-event.html#fnref2)
3.  关于 String 的 Topic 说明。<https://ethereum.stackexchange.com/questions/6840/indexed-event-with-string-not-getting-logged> [↩](http://me.tryblockchain.org/blockchain-solidity-event.html#fnref3)
