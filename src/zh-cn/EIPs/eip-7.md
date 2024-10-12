---
eip: 7
title: DELEGATECALL
author: Vitalik Buterin (@vbuterin)
status: Final
type: Standards Track
category: Core
created: 2015-11-15
---

### 硬分叉
[Homestead](./eip-606.md)

### 参数
- 激活：
  - 主网区块 >= 1,150,000
  - Morden 区块 >= 494,000
  - 未来测试网区块 >= 0

### 概述

添加一个新的操作码 `DELEGATECALL`，其值为 `0xf4`，其思想类似于 `CALLCODE`，但它将发送者和价值从父作用域传播到子作用域，即创建的调用具有与原始调用相同的发送者和价值。

### 规范

`DELEGATECALL`：`0xf4`，接受 6 个操作数：
- `gas`：代码执行所需的 gas 量；
- `to`：要执行代码的目标地址；
- `in_offset`：输入在内存中的偏移量；
- `in_size`：输入的字节大小；
- `out_offset`：输出在内存中的偏移量；
- `out_size`：输出的临时存储区大小。

#### 关于 gas 的说明
- 不提供基本津贴；`gas` 是被调用者接收的总量。
- 与 `CALLCODE` 一样，账户创建不会发生，因此前期 gas 成本始终为 `schedule.callGas` + `gas`。
- 未使用的 gas 会正常退款。

#### 关于发送者的说明
- `CALLER` 和 `VALUE` 在被调用者的环境中表现与在调用者的环境中完全相同。

#### 其他说明
- 深度限制 1024 仍然正常保留。

### 理由

将发送者和价值从父作用域传播到子作用域使得合约更容易将另一个地址存储为可变的代码源，并“传递”调用给它，因为子代码将在与父代码基本相同的环境中执行（除了减少的 gas 和增加的调用栈深度）。

用例 1：拆分代码以绕过 3m gas 限制

```python
~calldatacopy(0, 0, ~calldatasize())
if ~calldataload(0) < 2**253:
    ~delegate_call(msg.gas - 10000, $ADDR1, 0, ~calldatasize(), ~calldatasize(), 10000)
    ~return(~calldatasize(), 10000)
elif ~calldataload(0) < 2**253 * 2:
    ~delegate_call(msg.gas - 10000, $ADDR2, 0, ~calldatasize(), ~calldatasize(), 10000)
    ~return(~calldatasize(), 10000)
...
```

用例 2：存储合约代码的可变地址：

```python
if ~calldataload(0) / 2**224 == 0x12345678 and self.owner == msg.sender:
    self.delegate = ~calldataload(4)
else:
    ~delegate_call(msg.gas - 10000, self.delegate, 0, ~calldatasize(), ~calldatasize(), 10000)
    ~return(~calldatasize(), 10000)
```
这些方法调用的子函数现在可以自由引用 `msg.sender` 和 `msg.value`。

### 可能的反对意见

* 你可以通过将发送者放入调用数据的前 20 个字节来复制此功能。然而，这意味着代码需要为委托合约特别编译，并且不能同时在委托和原始上下文中使用。