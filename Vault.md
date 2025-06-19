### 第九关 Vault
### 难度2/5
## 这一关的通关条件猜对密码打开保险柜。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Vault {
    bool public locked;
    bytes32 private password;

    constructor(bytes32 _password) {
        locked = true;
        password = _password;
    }

    function unlock(bytes32 _password) public {
        if (password == _password) {
            locked = false;
        }
    }
}
```
区块链的一个特点就是数据公开透明。合约将password存储在一个private的状态变量里，可以通过区块链的底层接口直接获取。之后调用unlock函数送入password就可以解锁了。
### 注意点：
1. 以太坊合约的状态变量按照声明顺序分配到存储槽（从存储槽0开始）。
2. 每个存储槽可以容纳32字节，也就是256位数据。
## 解题步骤：
1. 
2.打开remix，新建攻击合约：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
contract attack{
    bool public locked;
    bytes32 public password ;
    function unlock(bytes32 _password)  public {
        if (password == _password) {
            locked = false;
    }
  }
    
}
```
3. 调用unlock函数的时候送入步骤1得到的password即可。
4. 提交过关。
