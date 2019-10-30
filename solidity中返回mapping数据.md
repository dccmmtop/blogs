---
tags: [solidity]
---

批量访问列表/数组/等在 Solidity 中很痛苦。你很少在合同中看到它。在您的情况下，一种可能的解决方案是提供一个函数来访问一个项目，使用它的索引，并让调用者从 0 循环到 id。

### demo

```solidity
//得到用户存款信息
function getInfoByUser(address user) public view returns(uint[],uint[]){
  uint[] memory money = new uint[](bank[user].depositRecordAmounts);
  uint[] memory time = new uint[](bank[user].depositRecordAmounts);
  for(uint i = 0; i< bank[user].depositRecordAmounts; i++){
    money[i] = bank[user].depositRecord[i].money;
    time[i] = bank[user].depositRecord[i].time;
  }
  return(money,time);
}
```
