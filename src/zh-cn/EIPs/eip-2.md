---
eip: 2
title: Homestead 硬分叉变更
author: Vitalik Buterin (@vbuterin)
status: Final
type: Standards Track
category: Core
created: 2015-11-15
---

### 元参考

[Homestead](./eip-606.md)。

### 参数

|   FORK_BLKNUM   | CHAIN_NAME  |
|-----------------|-------------|
|    1,150,000    | 主网       |
|   494,000       | Morden      |
|    0            | 未来测试网  |

# 规范

如果 `block.number >= HOMESTEAD_FORK_BLKNUM`，则执行以下操作：

1. 通过交易创建合约的 gas 成本从 21,000 增加到 53,000，即如果你发送一笔交易且目标地址为空字符串，则初始扣除的 gas 为 53,000 加上 tx 数据的 gas 成本，而不是当前的 21,000。通过 `CREATE` 操作码从合约创建合约不受影响。
2. 所有 s 值大于 `secp256k1n/2` 的交易签名现在被视为无效。ECDSA recover 预编译合约保持不变，并将继续接受高 s 值；这在合约恢复旧比特币签名时非常有用。
3. 如果合约创建没有足够的 gas 来支付将合约代码添加到状态的最终 gas 费用，则合约创建失败（即超出 gas 限制），而不是留下一个空合约。
4. 将难度调整算法从当前公式：`block_diff = parent_diff + parent_diff // 2048 * (1 if block_timestamp - parent_timestamp < 13 else -1) + int(2**((block.number // 100000) - 2))`（其中 `int(2**((block.number // 100000) - 2))` 表示指数难度调整组件）更改为 `block_diff = parent_diff + parent_diff // 2048 * max(1 - (block_timestamp - parent_timestamp) // 10, -99) + int(2**((block.number // 100000) - 2))`，其中 `//` 是整数除法运算符，例如 `6 // 2 = 3`，`7 // 2 = 3`，`8 // 2 = 4`。`minDifficulty` 仍然定义允许的最小难度，任何调整都不能将其降低到此值以下。

# 理由

目前，通过交易创建合约的激励过高，成本为 21,000，而通过合约创建的成本为 32,000。此外，借助自杀退款，目前可以仅使用 11,664 gas 进行简单的以太值转移；执行此操作的代码如下：

```python
from ethereum import tester as t
> from ethereum import utils
> s = t.state()
> c = s.abi_contract('def init():\n suicide(0x47e25df8822538a8596b28c637896b4d143c351e)', endowment=10**15)
> s.block.get_receipts()[-1].gas_used
11664
> s.block.get_balance(utils.normalize_address(0x47e25df8822538a8596b28c637896b4d143c351e))
1000000000000000
```
这并不是一个特别严重的问题，但可以说这是一个 bug。

允许任何 s 值在 `0 < s < secp256k1n` 的交易，如当前情况，打开了交易可塑性问题，因为可以将任何交易的 s 值从 `s` 翻转为 `secp256k1n - s`，翻转 v 值（`27 -> 28`，`28 -> 27`），结果签名仍然有效。这并不是一个严重的安全缺陷，尤其是以太坊使用地址而不是交易哈希作为以太值转移或其他交易的输入，但它确实造成了用户界面的不便，因为攻击者可以导致在区块中确认的交易与任何用户发送的交易具有不同的哈希，从而干扰使用交易哈希作为跟踪 ID 的用户界面。防止高 s 值消除了这个问题。

如果没有足够的 gas 来支付最终的 gas 费用，使合约创建超出 gas 限制的好处在于：
- (i) 它在合约创建过程中创建了更直观的“成功或失败”区分，而不是当前的“成功、失败或空合约”三分法；
- (ii) 使失败更容易检测，因为除非合约创建完全成功，否则根本不会创建合约账户；并且
- (iii) 在存在捐赠的情况下，使合约创建更安全，因为可以保证整个初始化过程要么发生，要么交易失败并且捐赠被退还。

难度调整的变化最终解决了以太坊协议在两个月前看到的问题，即过多的矿工在挖掘包含时间戳等于 `parent_timestamp + 1` 的区块；这扭曲了区块时间分布，因此当前的区块时间算法，目标是 *中位数* 为 13 秒，继续以相同的中位数为目标，但均值开始增加。如果 51% 的矿工开始以这种方式挖掘区块，均值将增加到无穷大。提出的新公式大致基于目标均值；可以证明，使用当前公式，长期内平均区块时间超过 24 秒在数学上是不可能的。

使用 `(block_timestamp - parent_timestamp) // 10` 作为主要输入变量而不是时间差直接有助于保持算法的粗粒度特性，防止过度激励将时间戳差设置为精确的 1，以便创建一个稍微更高难度的区块，从而保证击败任何可能的分叉。-99 的上限仅用于确保如果由于客户端安全漏洞或其他黑天鹅事件，两个区块在时间上相距非常远，难度不会极度下降。

# 实现

这在 Python 中实现如下：

1. https://github.com/ethereum/pyethereum/blob/d117c8f3fd93359fc641fd850fa799436f7c43b5/ethereum/processblock.py#L130
2. https://github.com/ethereum/pyethereum/blob/d117c8f3fd93359fc641fd850fa799436f7c43b5/ethereum/processblock.py#L129
3. https://github.com/ethereum/pyethereum/blob/d117c8f3fd93359fc641fd850fa799436f7c43b5/ethereum/processblock.py#L304
4. https://github.com/ethereum/pyethereum/blob/d117c8f3fd93359fc641fd850fa799436f7c43b5/ethereum/blocks.py#L42