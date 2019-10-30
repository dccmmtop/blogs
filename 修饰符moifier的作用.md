---
tags: [soliity，contract]
---

[摘自于 stackflow](https://stackoverflow.com/questions/44982437/what-does-a-modifier-do-in-solidity)
修饰符允许您将其他功能包装到方法中，因此它们有点像 OOP 中的装饰器模式。

修饰符通常用于智能合约中，以确保在继续执行方法中的其余代码之前满足某些条件

例如，isOwner 通常用于确保方法的调用者是合同的所有者：

```js
modifier isOwner() {
   if (msg.sender != owner) {
        throw;
    }

    _; // continue executing rest of method body
}

doSomething() isOwner { //直接写在方法后面作为函数的一部分
  // will first check if caller is owner

  // code
}
```

您还可以堆叠多个修改器以简化您的过程

```js
enum State { Created, Locked, Inactive }

modifier isState(State _state) {
    require(state == _state);

    _; // run rest of code
}

modifier cleanUp() {
    _; // finish running rest of method body

    // clean up code
}

doSomething() isOwner isState(State.Created) cleanUp {
  // code
}
```

修饰符以声明和可读的方式表达正在发生的操作。
