### 第十五关 Naught Coin
### 难度3/5
## 这一关的通关条件是将代币余额变为0。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "openzeppelin-contracts-08/token/ERC20/ERC20.sol";

contract NaughtCoin is ERC20 {
    // string public constant name = 'NaughtCoin';
    // string public constant symbol = '0x0';
    // uint public constant decimals = 18;
    uint256 public timeLock = block.timestamp + 10 * 365 days;
    uint256 public INITIAL_SUPPLY;
    address public player;

    constructor(address _player) ERC20("NaughtCoin", "0x0") {
        player = _player;
        INITIAL_SUPPLY = 1000000 * (10 ** uint256(decimals()));
        // _totalSupply = INITIAL_SUPPLY;
        // _balances[player] = INITIAL_SUPPLY;
        _mint(player, INITIAL_SUPPLY);
        emit Transfer(address(0), player, INITIAL_SUPPLY);
    }

    function transfer(address _to, uint256 _value) public override lockTokens returns (bool) {
        super.transfer(_to, _value);
    }

    // Prevent the initial owner from transferring tokens until the timelock has passed
    modifier lockTokens() {
        if (msg.sender == player) {
            require(block.timestamp > timeLock);
            _;
        } else {
            _;
        }
    }
}
```
仔细观察，合约中的transfer函数增加了modifier用来锁定代币十年，并且合约override了transfer函数。但是对于ERC20代币，还可以使用transferFrom函数用来转移代币。transferFrom是ERC20标准中定义的一个函数，允许一个地址（通常是授权的spender）从另一个地址（owner）转移一定数量的代币到目标地址。所以我们先approve一下，然后再将代币发送到目标地址。
## 解题步骤：
1. 打开console，输入：
```Javascript
contract.approve(player,toWei("1000000"));
```
2. 调用transferFrom
```Javascript
contract.transferFrom(player,leveladdress,toWei("1000000"));
```
3. 提交过关
