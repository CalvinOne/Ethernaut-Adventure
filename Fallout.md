### 第三关 Fallout
### 难度1/5
## 这一关的通关条件是成为合约的owner。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "openzeppelin-contracts-06/math/SafeMath.sol";

contract Fallout {
    using SafeMath for uint256;

    mapping(address => uint256) allocations;
    address payable public owner;

    /* constructor */
    function Fal1out() public payable {
        owner = msg.sender;
        allocations[owner] = msg.value;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "caller is not the owner");
        _;
    }

    function allocate() public payable {
        allocations[msg.sender] = allocations[msg.sender].add(msg.value);
    }

    function sendAllocation(address payable allocator) public {
        require(allocations[allocator] > 0);
        allocator.transfer(allocations[allocator]);
    }

    function collectAllocations() public onlyOwner {
        msg.sender.transfer(address(this).balance);
    }

    function allocatorBalance(address allocator) public view returns (uint256) {
        return allocations[allocator];
    }
}
```
这关很简单，仔细观察合约的constructor函数写错了，写成了Fal1out()。这一关的设计灵感来源于现实世界中的智能合约漏洞案例，Rubixi事件，可以自行搜索下。不过现在合约的构造函数都使用constructor关键字来定义，这种错误不复存在。
## 解题步骤：
1. 调用Fal1out()函数。
```javascript
await contract.Fal1out()
```
2. 提交通过关卡。
