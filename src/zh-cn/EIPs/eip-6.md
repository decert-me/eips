---
eip: 6
title: 重命名 SUICIDE 操作码
author: Hudson Jameson <hudson@hudsonjameson.com>
status: Final
type: Standards Track
category: Interface
created: 2015-11-22
---

### 摘要
本 EIP 提出的解决方案是将以太坊编程语言中的 `SUICIDE` 操作码更名为 `SELFDESTRUCT`。

### 动机
心理健康对许多人来说是一个非常现实的问题，细微的变化可以带来不同。那些经历失落或抑郁的人会受益于在我们的编程语言中不看到“自杀”这个词。根据一些估计，全球有 3.5 亿人受到抑郁症的影响。如果我们希望将我们的生态系统扩展到所有类型的开发者，就需要经常审查以太坊编程语言的语义。

由 DEVolution, GmbH 委托的以太坊安全审计和 [Least Authority 执行的审计](https://github.com/LeastAuthority/ethereum-analyses/blob/master/README.md) 推荐如下：
> 将指令名称“suicide”替换为“self-destruct”、“destroy”、“terminate”或“close”等较少带有负面含义的词，特别是因为这是描述合约自然结束的术语。

我们更改“自杀”一词的主要原因是表明人比代码更重要，以太坊作为一个成熟的项目，应该认识到变更的必要性。自杀是一个沉重的话题，我们应该尽一切可能不影响我们开发社区中那些遭受抑郁或最近失去自杀者的人。以太坊是一个年轻的平台，如果我们在其早期实施这一变更，将会减少许多麻烦。

### 实施
`SELFDESTRUCT` 被添加为 `SUICIDE` 操作码的别名（而不是替换它）。
https://github.com/ethereum/solidity/commit/a8736b7b271dac117f15164cf4d2dfabcdd2c6fd
https://github.com/ethereum/serpent/commit/1106c3bdc8f1bd9ded58a452681788ff2e03ee7c