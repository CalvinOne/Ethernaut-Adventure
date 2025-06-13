### 第五关 Telephone
### 难度1/5
## 这一关的通关条件增加自己的地址里的token数量。
## 代码如下：
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {
    mapping(address => uint256) balances;
    uint256 public totalSupply;

    constructor(uint256 _initialSupply) public {
        balances[msg.sender] = totalSupply = _initialSupply;
    }

    function transfer(address _to, uint256 _value) public returns (bool) {
        require(balances[msg.sender] - _value >= 0);
        balances[msg.sender] -= _value;
        balances[_to] += _value;
        return true;
    }

    function balanceOf(address _owner) public view returns (uint256 balance) {
        return balances[_owner];
    }
}
```
看合约里的加加减减以及增加token数量的过关条件，猜测本关与溢出漏洞有关。首先查看我们的余额有20个token，所以只要把我们的token余额增加大于20就可以了。我们来看看这个合约里潜在的漏洞：由于balances[msg.sender]和_value都是uint类型balances[msg.sender] - _value的结果也是uint，作为无符号整数永远是大于等于 0 的，所以这句校验相当于是无效的。正确的写法是require(balances[msg.sender] >= _value)。
## 解题步骤：
1. 直接执行transfer函数，根据代码，送入的参数不是自己的钱包地址，value为21即可。这里就输入levelInstance地址就行了，控制台输入：
```Javascript
contract.transfer(levelInstance,21)
```
2. 提交过关。
