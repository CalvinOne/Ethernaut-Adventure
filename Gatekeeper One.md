### 第十三关 Gatekeeper One
### 难度4/5
## 这一关的通关条件是通过三道modifier。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract GatekeeperOne {
    address public entrant;

    modifier gateOne() {
        require(msg.sender != tx.origin);
        _;
    }

    modifier gateTwo() {
        require(gasleft() % 8191 == 0);
        _;
    }

    modifier gateThree(bytes8 _gateKey) {
        require(uint32(uint64(_gateKey)) == uint16(uint64(_gateKey)), "GatekeeperOne: invalid gateThree part one");
        require(uint32(uint64(_gateKey)) != uint64(_gateKey), "GatekeeperOne: invalid gateThree part two");
        require(uint32(uint64(_gateKey)) == uint16(uint160(tx.origin)), "GatekeeperOne: invalid gateThree part three");
        _;
    }

    function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
        entrant = tx.origin;
        return true;
    }
}
```
所谓的gate就是指合约中的modifier，关卡合约里一共三个modifier。只要通过这三个条件就可以过关了，我们一个个看：
### Gate One
这个修饰器中的作用是校验msg.sende与tx.origin不相等。所以用EOA账户调用攻击合约发起交易就可以做到这点。
### Gate Two
知识点：gasleft()是Solidity提供的内置函数，用于返回当前交易或函数调用中剩余的Gas数量（以wei为单位）。所以这里的要求是剩余gas必须是8191的整数倍才行。这里使用一个for循环以及8191 * 3，使得gas更加接近可能是因为它接近典型的gas消耗范围（20,000–30,000）。
```solidity
for (uint256 i = 0; i < 8191; i++) {
    if (address(_param).call.gas(i + 8191 * 3)(bytes4(keccak256("enter(bytes8)")), _gateKey)) {
        return true;
    }
}
```
### Gate Three
要通过这个修饰器，我们需要构造一个bytes8类型的_gateKey，使其满足修饰器中的三个require条件。
- uint64(_gateKey) 的低32位等于其低16位，例如，如果低16位是0xABCD，则低32位必须是0xABCDABCD。
- 低32位不等于整个64位值，uint64(_gateKey)的高32位不能全为0，否则uint64(_gateKey)等于其低32位。因此，uint64(_gateKey) 的高32位必须至少有一位非0。
- uint64(_gateKey)的低32位必须等于tx.origin的低16位，uint64(_gateKey) 的低 32 位必须与 tx.origin 的低16位一致。
所以bytes8 _gateKey = bytes8(tx.origin) & 0xFFFFFFFF0000FFFF；
## 解题步骤：
