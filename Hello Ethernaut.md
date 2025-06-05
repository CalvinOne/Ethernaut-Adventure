### 第一关 Hello Ethernaut
### 难度0/5
## 这关是新手村教学，让玩家做一个基础的入门，并了解靶场的玩法机制。
1. 首先，需要安装MetaMask浏览器插件。如果尚未安装，请前往浏览器扩展商店下载并安装。安装完成后，设置好你的钱包，并在插件界面的左上角选择网络（network selector），切换到目标网络。如果选择了不受支持的网络，游戏会提示，并自动将网络切换到默认的 Sepolia 测试网络。
2. 打开浏览器的开发者工具：点击“工具” > “开发者工具”，（快捷键通常是 F12 或 Ctrl+Shift+I）。打开打开Developer Console后，能看到游戏输出的一些信息，输入“player”，可以看到用户自己的钱包地址，此外，记得留意控制台中的警告和错误信息，它们可能会在闯关时提供关键线索。
```javascript
await player
```
3. 可以通过以下命令查看当前账户的以太币余额，注意：直接输入getBalance(player)执行后可能显示“pending”（待处理），需要展开Promise才能看到实际余额。如果在Chrome v62或更高版本上，可以使用 await getBalance(player) 获取更清晰的输出。
```javascript
await getBalance(player)
```
4. 输入以下命令查看所有可用功能，了解更多控制台辅助工具,这些工具在闯关过程中会非常有用，建议熟悉一下。
```javascript
await help()
```
5. ethernaut是游戏的核心智能合约对象。虽然通常不需要通过控制台直接与它交互（游戏界面会帮助处理），但现在探索这个对象是个绝佳的学习机会。通过它，可以了解如何与其他智能合约交互。
6. 要参与链上交互，就需要eth作为gas fee，这里推荐使用Alchemy的水龙头领取测试eth，每72小时可以领取一次。
7. Ethernaut中，不会直接与ethernaut合约交互，而是通过它生成一个关卡实例（level instance）。点击页面底部的“Get New Instance”（获取新实例）按钮，生成一个关卡实例。点击后，MetaMask 会提示授权交易，确认后，控制台会显示相关消息。注意，这会部署一个新合约到区块链上，可能需要几秒钟，耐心等待即可。
8. 根据提示输入contract.info()或者await contract.info()，一步步根据提示可以通过这关,最后会弹出一个MetaMask的交易请求，确认后再按Submit instance，再确认后 Console 会弹出 “You have completed this level!!!” 即本关完成。
```javascript
> await contract.info()
< 'You will find what you need in info1().'
> await contract.info1()
< 'Try info2(), but with "hello" as a parameter.'
> await contract.info2("hello")
< 'The property infoNum holds the number of the next info method to call.'
> await contract.infoNum()
< i {negative: 0, words: Array(2), length: 1, red: null}
> await contract.info42()
< 'theMethodName is the name of the next method.'
> await contract.theMethodName()
< 'The method name is method7123949.'
> await contract.method7123949()
< 'If you know the password, submit it to authenticate().'
> await contract.password()
< 'ethernaut0'
> await contract.authenticate("ethernaut0")
```
