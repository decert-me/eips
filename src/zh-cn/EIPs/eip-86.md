---
eip: 86
title: 交易来源和签名的抽象
author: Vitalik Buterin (@vbuterin)
type: Standards Track
category: Core
status: Stagnant
created: 2017-02-10
---

# 摘要

实现了一组变更，旨在“抽象出”签名验证和随机数检查，允许用户创建“账户合约”，以执行任何所需的签名/随机数检查，而不是使用当前硬编码到交易处理中的机制。

# 参数

* METROPOLIS_FORK_BLKNUM: 待定
* CHAIN_ID: 与 EIP 155 使用的相同（即主网为 1，测试网为 3）
* NULL_SENDER: 2**160 - 1

# 规范

如果 `block.number >= METROPOLIS_FORK_BLKNUM`，则：
1. 如果交易的签名为 `(CHAIN_ID, 0, 0)`（即 `r = s = 0`，`v = CHAIN_ID`），则将其视为有效，并将发送者地址设置为 `NULL_SENDER`
2. 这种形式的交易必须具有 gasprice = 0，nonce = 0，value = 0，并且不增加账户 NULL_SENDER 的随机数。
3. 在 `0xfb` 处创建一个新的操作码 `CREATE2`，具有 4 个栈参数（value, salt, mem_start, mem_size），其创建地址设置为 `sha3(sender + salt + sha3(init code)) % 2**160`，其中 `salt` 始终表示为 32 字节的值。
4. 对所有合约创建操作，包括交易和操作码，添加规则：如果该地址的合约已经存在并且具有非空代码或非空随机数，则操作失败并返回 0，仿佛初始化代码已耗尽 gas。如果一个账户的代码和随机数为空但余额非空，则创建操作仍然可以成功。

# 理由

这些变更的目标是为账户安全的抽象奠定基础。我们不再在协议中将 ECDSA 和默认随机数方案视为唯一的“标准”方式来保护账户，而是朝着一个长期目标迈出初步步骤，即所有账户都是合约，合约可以支付 gas，用户可以自由定义自己的安全模型。

在 EIP 86 下，我们可以预期用户将其以太存储在合约中，其代码可能如下所示（示例为 Serpent）：

```python
# Get signature from tx data
sig_v = ~calldataload(0)
sig_r = ~calldataload(32)
sig_s = ~calldataload(64)
# Get tx arguments
tx_nonce = ~calldataload(96)
tx_to = ~calldataload(128)
tx_value = ~calldataload(160)
tx_gasprice = ~calldataload(192)
tx_data = string(~calldatasize() - 224)
~calldataload(tx_data, 224, ~calldatasize())
# Get signing hash
signing_data = string(~calldatasize() - 64)
~mstore(signing_data, tx.startgas)
~calldataload(signing_data + 32, 96, ~calldatasize() - 96)
signing_hash = sha3(signing_data:str)
# Perform usual checks
prev_nonce = ~sload(-1)
assert tx_nonce == prev_nonce + 1
assert self.balance >= tx_value + tx_gasprice * tx.startgas
assert ~ecrecover(signing_hash, sig_v, sig_r, sig_s) == <pubkey hash here>
# Update nonce
~sstore(-1, prev_nonce + 1)
# Pay for gas
~send(MINER_CONTRACT, tx_gasprice * tx.startgas)
# Make the main call
~call(msg.gas - 50000, tx_to, tx_value, tx_data, len(tx_data), 0, 0)
# Get remaining gas payments back
~call(20000, MINER_CONTRACT, 0, [msg.gas], 32, 0, 0)
```

这可以被视为一个“转发合约”。它接受来自“入口点”地址 2**160 - 1（任何人都可以从中发送交易的账户）发送的数据，期望数据格式为 `[sig, nonce, to, value, gasprice, data]`。转发合约验证签名，如果签名正确，则设置对矿工的支付，然后将调用发送到所需地址，附带提供的值和数据。

这提供的好处在于最有趣的案例：

- **多重签名钱包**：目前，从多重签名钱包发送需要每个操作都得到参与者的批准，每个批准都是一笔交易。这可以通过让一个批准交易包含其他参与者的签名来简化，但即便如此，它也引入了复杂性，因为参与者的账户都需要存有 ETH。通过这个 EIP，将可以让合约存储 ETH，直接向合约发送包含所有签名的交易，合约可以支付费用。
- **环签名混合器**：环签名混合器的工作方式是 N 个人将 1 个币发送到合约中，然后使用可链接的环签名稍后提取 1 个币。可链接的环签名确保提取交易无法与存款关联，但如果有人尝试提取两次，则这两个签名可以关联，第二个提取将被阻止。然而，目前存在隐私风险：提取时需要有币来支付 gas，如果这些币没有正确混合，则可能会危及隐私。通过这个 EIP，您可以直接用提取的币支付 gas。
- **自定义密码学**：用户可以根据自己的条件升级到 ed25519 签名、Lamport 哈希阶梯签名或其他任何他们想要的方案；他们不需要坚持使用 ECDSA。
- **非密码学修改**：用户可以要求交易具有过期时间（这将使旧的空账户/尘埃账户能够安全地从状态中清除），使用 k-并行随机数（允许交易稍微无序确认的方案，减少交易间依赖），或进行其他修改。

(2) 和 (3) 引入了类似于比特币 P2SH 的功能，允许用户将资金发送到可证明仅映射到特定代码的地址。这样的功能在长期内至关重要，因为在一个所有账户都是合约的世界中，我们需要保留在该账户在链上存在之前发送到该账户的能力，因为这是所有区块链协议中存在的基本功能。

# 矿工和交易重放策略

请注意，矿工需要有一个策略来接受这些交易。这个策略需要非常严格，因为否则他们可能会接受不支付任何费用的交易，甚至可能接受没有效果的交易（例如，因为交易已经包含，因此随机数不再有效）。

一个简单的策略是有一组正则表达式，账户的目标地址将与之进行检查，每个正则表达式对应于已知为“安全”的“标准账户类型”（在这种情况下，如果账户具有该代码，并且涉及账户余额、账户存储和交易数据的特定检查通过，则如果交易包含在区块中，矿工将获得支付），并挖掘和转发通过这些检查的交易。

一个例子是检查如下：

1. 检查目标地址的代码是否为上述 Serpent 代码的编译版本，`<pubkey hash here>` 替换为任何公钥哈希。
2. 检查交易数据中的签名是否与该密钥哈希验证。
3. 检查交易数据中的 gasprice 是否足够高
4. 检查状态中的随机数是否与交易数据中的随机数匹配
5. 检查账户中是否有足够的以太来支付费用

如果所有五个检查都通过，则转发和/或挖掘该交易。

一个更宽松但仍然有效的策略是接受任何符合上述一般格式的代码，仅消耗有限的 gas 来执行随机数和签名检查，并保证交易费用将支付给矿工。另一种策略是，结合其他方法，尝试处理任何请求少于 250,000 gas 的交易，并仅在执行交易后矿工的余额适当增加时才包括它。

# 版权

版权及相关权利通过 CC0 放弃。