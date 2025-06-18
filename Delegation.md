### 第七关 Delegation
### 难度2/5
## 这一关的通关条件是获得Delegation合约所有权。
## 代码如下：
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Delegate {
    address public owner;

    constructor(address _owner) {
        owner = _owner;
    }

    function pwn() public {
        owner = msg.sender;
    }
}

contract Delegation {
    address public owner;
    Delegate delegate;

    constructor(address _delegateAddress) {
        delegate = Delegate(_delegateAddress);
        owner = msg.sender;
    }

    fallback() external {
        (bool result,) = address(delegate).delegatecall(msg.data);
        if (result) {
            this;
        }
    }
}
```
这一关有两个合约，Delegate和Delegation。
## Delegate
Delegate就一个pwn函数，简单粗暴的将msg.sender更改为owner。
## Delegation
1. Delegation合约对合约Delegate进行了引用，同样的也只有一个函数-fallback()。fallback函数是在特定情况下自动触发的。一就是合约接收ETH但没有receive函数的时候，而就是调用不存在的函数的时候。
2. fallback函数里使用了delegatecall方法。这里提一下delegatecall与call的区别。
### call ：
- 调用目标合约的函数时，使用目标合约的存储和上下文。
- 调用者的代码在目标合约中运行，修改的是目标合约的存储变量。
- `msg.sender` 是调用者的地址（即发起 `call` 的合约或用户），`msg.value` 是发送的以太币。

### delegatecall ：
- 调用目标合约的函数时，使用调用者合约的存储和上下文。
- 目标合约的代码在调用者合约的上下文中运行，修改的是调用者合约的存储。
- `msg.sender` 和 `msg.value` 保持原始调用者的值（即最初调用者的地址和发送的以太币）。
### 总的来说就是：1、当用户A通过合约B来delegatecall合约C的时候，执行的是合约C的函数，但是语境仍是合约B的：msg.sender是A的地址，并且如果函数改变一些状态变量，产生的效果会作用于合约B的变量上。2、当用户A通过合约B来call合约C的时候，执行的是合约C的函数，语境是合约C的：msg.sender是B的地址，并且如果函数改变一些状态变量，产生的效果会作用于合约C的变量上。

## 解题步骤：
1. 打开remix线上编辑器,把Delegate合约的源码贴过去。在compiler界面点击compilation details，再点开FUNCTIONHASHES，就可以找到pwn函数的签名“dd365b8b”。
2.  打开console，直接输入：
```Javascript
contract.sendTransaction({data:'dd365b8b'})
```
3. 提交过关。
