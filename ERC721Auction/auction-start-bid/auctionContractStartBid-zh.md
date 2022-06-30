在本章节，我们会创建一个用于启动拍卖的函数和一个用于竞价的函数

### Start
在拍卖者启动拍卖之前，我们会先检查一些必要条件是否满足。

首先，我们检查一下拍卖是否已经开始（第 49 行）。如果拍卖已经开始了，并且我们的状态变量 `started` 是 `true`，我们就停止执行当前函数并且抛出一个异常。

第二个条件是，我们会检查拍卖者是否正在执行当前函数（第 50 行）。我们已经创建了一个函数，用于在卖家部署合约的时候将卖家的地址保存在状态变量 `seller` 中，所以我们现在可以检查这个函数当前的调用者是否是当初部署该合约的卖家，如果不是的话，就抛出一个异常。

接下来，我们要将卖家拍卖的 NFT 转移到当前合约（第 52 行）。
我们将状态`started` 设置为 `true`（第 53 行），并为此次拍卖设置一个截止日期（第 54 行）。在这里，我们把截止日期设置为开始拍卖的 7 天后。我们通过在数字后面加上后缀 `days` 用来指定时间单位。如果你想要了解更多关于时间单位的内容，可以访问 <a href="https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units" target="_blank">solidity documentation</a>

最后，我们会发送 `Start()` 事件。

### Bid
在该函数被调用之前，我们需要确保以下条件得到了满足：此次拍卖已经开始（第 60 行），此次拍卖还未结束（第 61 行），此次竞价（本次函数调用传递的金额）要高于当前给出的最高出价（第 62 行）。

现在，我们需要在给出新的出价之前保存当前最高的出价。首先，我们检查当前是否有人出价。如果该函数是第一次被调用，那么下一行代码就会被忽略。在我们的 mapping `bids` （第 34 行）中，我们做了一个从键（出价者的地址）到值（出价者在退出之前在拍卖中出价的 ETH 总量）的映射。如果有出价者，我们将最高出价者的最后出价（`highestBid`）加到所有出价的总价值中（第 65 行），然后退出。我们存储出价是因为如果出价者不再是最高出价者，我们希望让出价者能够撤回他们用来出价的 ETH。

最后，我们将变量 `highestBidder` 设置为调用该函数的账户地址（第 68 行），将变量 `highestBid` 设置为他们的出价，即此次调用的 msg.value（第 69 行）。

最后我们发送 Bid 事件（第 71 行）

## ⭐️ 练习实操
1. 部署一个 NFT 合约。你可以使用我们在 "Solidity NFT Course" 这一章节中创建的智能合约。

2. 给自己分配一个 tokenId 为 0 的 NFT。

3. 部署这个 EnglishAuction 合约。将参数 `_nft` 设置为上一步部署的 NFT 合约的地址，将参数 `_nftId` 设置为 0，参数 `_startingBid` 设为 1。

4. 调用 NFT 合约的 `approve` 方法，将参数 `to` 设置为拍卖合约的地址，将参数 `tokenId` 设置为 0。这样做的目的是允许这个合约可以把此次要拍卖的代币转移出去。

5. 调用拍卖合约的 `start` 方法。如果你现在调用 `started` 方法，它应该返回 `true`。如果你调用 `highestBid` 函数，它应该返回 1。

6. 调用合约的 `bid` 方法，并将交易价值设置为 3 wei。此时再调用 `highestBid` 方法，他应该返回 3。