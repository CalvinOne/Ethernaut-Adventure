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
区块链的一个特点就是数据公开透明。合约将password存储在一个private的变量里，是可以在链上看到这个存储的值。之后调用unlock函数送入password就可以解锁了。
