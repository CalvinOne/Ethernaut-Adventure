### 第十四关 Gatekeeper Two
### 难度3/5
## 这一关的通关条件是通过三道modifier。
## 代码如下：
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract GatekeeperTwo {
    address public entrant;

    modifier gateOne() {
        require(msg.sender != tx.origin);
        _;
    }

    modifier gateTwo() {
        uint256 x;
        assembly {
            x := extcodesize(caller())
        }
        require(x == 0);
        _;
    }

    modifier gateThree(bytes8 _gateKey) {
        require(uint64(bytes8(keccak256(abi.encodePacked(msg.sender)))) ^ uint64(_gateKey) == type(uint64).max);
        _;
    }

    function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
        entrant = tx.origin;
        return true;
    }
}
```
### gateOne
EOA账户使用合约调用关卡合约即可。
### gateTwo
要求caller的code大小必须为0，当构造函数执行初始化合约存储时，它会返回运行时字节码。直到构造函数的结束，合约本身没有任何运行时字节码，这意味着如果你此时调用address(contract).code.length，它将返回0。
### gateThree
需要msg.sender ^ gateKey = 0-1，則可以msg.sender ^ 0-1求得gateKey
## 解题步骤：
1. 打开remix，部署攻击合约：
```Solidity
// SPDX-License-Identifier: MIT
interface IGatekeeperTwo {
    function enter(bytes8 _gateKey) external returns (bool);
}

contract GatekeeperTwo {
    address levelInstance;
    
    constructor(address _levelInstance) {
      levelInstance = _levelInstance;
      unchecked{
          bytes8 key = bytes8(uint64(bytes8(keccak256(abi.encodePacked(this)))) ^ uint64(0) - 1  );
          IGatekeeperTwo(levelInstance).enter(key);
      }
    }
}
```
2. 提交过关。
