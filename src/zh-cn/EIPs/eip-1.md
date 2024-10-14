---
eip: 1
title: EIP 目的和指南
status: 进行中
type: Meta
author: Martin Becze <mb@ethereum.org>, Hudson Jameson <hudson@ethereum.org>, 等
created: 2015-10-27
---

## 什么是 EIP？

EIP 代表以太坊改进提案。EIP 是一份设计文档，向以太坊社区提供信息，或描述以太坊或其过程或环境的新特性。EIP 应提供特性的简明技术规范和特性的理由。EIP 作者负责在社区内建立共识并记录不同意见。

## EIP 理由

我们希望 EIP 成为提出新特性、收集社区技术意见和记录以太坊设计决策的主要机制。由于 EIP 作为文本文件保存在版本控制的仓库中，它们的修订历史是特性提案的历史记录。

对于以太坊的实现者来说，EIP 是跟踪其实现进度的便捷方式。理想情况下，每个实现维护者都会列出他们已实现的 EIP。这将为最终用户提供一种便捷的方式来了解给定实现或库的当前状态。

## EIP 类型

EIP 有三种类型：

- **标准跟踪 EIP** 描述任何影响大多数或所有以太坊实现的更改，例如——对网络协议的更改、对区块或交易有效性规则的更改、提议的应用标准/惯例，或任何影响使用以太坊的应用互操作性的更改或添加。标准跟踪 EIP 由三部分组成——设计文档、实现和（如有必要）对 [正式规范](https://github.com/ethereum/yellowpaper) 的更新。此外，标准跟踪 EIP 可以细分为以下类别：
  - **核心**：需要共识分叉的改进（例如 [EIP-5](./eip-5.md)、[EIP-101](./eip-101.md)），以及不一定是共识关键但可能与 [“核心开发”讨论](https://github.com/ethereum/pm) 相关的更改（例如 [EIP-90]，以及 [EIP-86](./eip-86.md) 的矿工/节点策略更改 2、3 和 4）。
  - **网络**：包括围绕 [devp2p](https://github.com/ethereum/devp2p/blob/readme-spec-links/rlpx.md) 的改进 ([EIP-8](./eip-8.md)) 和 [轻以太坊子协议](https://ethereum.org/en/developers/docs/nodes-and-clients/#light-node)，以及对 [whisper](https://github.com/ethereum/go-ethereum/issues/16013#issuecomment-364639309) 和 [swarm](https://github.com/ethereum/go-ethereum/pull/2959) 的网络协议规范的提议改进。
  - **接口**：包括围绕语言级标准的改进，如方法名称 ([EIP-6](./eip-6.md)) 和 [合约 ABI](https://docs.soliditylang.org/en/develop/abi-spec.html)。
  - **ERC**：应用级标准和惯例，包括合约标准，如代币标准 ([ERC-20](../ERCs/erc-20.md))、名称注册 ([ERC-137](./eip-137.md))、URI 方案、库/包格式和钱包格式。

- **元 EIP** 描述围绕以太坊的过程或提议对过程的更改（或事件）。过程 EIP 类似于标准跟踪 EIP，但适用于以太坊协议以外的领域。它们可能提议一个实现，但不涉及以太坊的代码库；它们通常需要社区共识；与信息性 EIP 不同，它们不仅仅是建议，用户通常不能随意忽视它们。示例包括程序、指南、决策过程的更改以及用于以太坊开发的工具或环境的更改。任何元 EIP 也被视为过程 EIP。

- **信息性 EIP** 描述以太坊设计问题，或向以太坊社区提供一般指南或信息，但不提议新特性。信息性 EIP 不一定代表以太坊社区的共识或建议，因此用户和实现者可以自由忽视信息性 EIP 或遵循其建议。

强烈建议单个 EIP 包含一个关键提案或新想法。EIP 越集中，成功的可能性越大。对一个客户端的更改不需要 EIP；对多个客户端有影响的更改，或为多个应用定义标准的更改，则需要。

EIP 必须满足某些最低标准。它必须是对提议增强的清晰和完整的描述。该增强必须代表净改进。提议的实现（如适用）必须是可靠的，并且不得过度复杂化协议。

### 核心 EIP 的特殊要求

如果 **核心** EIP 提到或提议对 EVM（以太坊虚拟机）的更改，则应至少一次引用其助记符并定义这些助记符的操作码。首选方式如下：

```
REVERT (0xfe)
```

## EIP 工作流程

### 监督 EIP

参与该过程的各方包括您，冠军或 *EIP 作者*，[*EIP 编辑*](#eip-editors) 和 [*以太坊核心开发者*](https://github.com/ethereum/pm)。

在您开始撰写正式 EIP 之前，您应该对您的想法进行审查。首先询问以太坊社区，看看这个想法是否是原创，以避免在基于先前研究被拒绝的事情上浪费时间。因此，建议在 [以太坊魔法师论坛](https://ethereum-magicians.org/) 上开启讨论线程。

一旦想法经过审查，您的下一个责任将是通过 EIP 向审阅者和所有相关方展示该想法，邀请编辑、开发者和社区在上述渠道上提供反馈。您应该尝试评估对您的 EIP 的兴趣是否与实施所需的工作量以及需要遵循它的各方数量相称。例如，实施核心 EIP 所需的工作将远大于 ERC，EIP 需要以太坊客户端团队的足够兴趣。负面的社区反馈将被考虑在内，并可能阻止您的 EIP 进入草案阶段。

### 核心 EIP

对于核心 EIP，鉴于它们需要客户端实现才能被视为 **最终**（见下文“EIP 过程”），您需要提供客户端的实现或说服客户端实现您的 EIP。

让客户端实现者审查您的 EIP 的最佳方式是在 AllCoreDevs 电话会议上展示它。您可以通过在 [AllCoreDevs 议程 GitHub 问题](https://github.com/ethereum/pm/issues) 上发布链接到您的 EIP 的评论来请求这样做。

AllCoreDevs 电话会议为客户端实现者提供了三件事。首先，讨论 EIP 的技术优点。其次，评估其他客户端将实施的内容。第三，协调网络升级的 EIP 实施。

这些电话会议通常会导致对应实施哪些 EIP 的“粗略共识”。这种“粗略共识”基于以下假设：EIP 不够有争议以导致网络分裂，并且它们在技术上是合理的。

:warning: EIP 过程和 AllCoreDevs 电话会议并不是为了处理有争议的非技术问题而设计的，但由于缺乏其他解决方式，往往会与这些问题纠缠在一起。这使得客户端实现者需要尝试评估社区情绪，从而妨碍 EIP 和 AllCoreDevs 电话会议的技术协调功能。如果您在监督 EIP，您可以通过确保 [以太坊魔法师论坛](https://ethereum-magicians.org/) 上与您的 EIP 相关的讨论线程包含或链接尽可能多的社区讨论，并确保各利益相关者得到充分代表，从而简化建立社区共识的过程。
*简而言之，您作为倡导者的角色是使用下面描述的风格和格式编写 EIP，在适当的论坛中引导讨论，并围绕该想法建立社区共识。*

### EIP 过程

以下是所有 EIP 在所有轨道中的标准化过程：

![EIP 状态图](../assets/eip-1/EIP-process-update.jpg)

**想法** - 一个尚未草拟的想法。此状态不在 EIP 存储库中跟踪。

**草稿** - EIP 开发的第一个正式跟踪阶段。当 EIP 格式正确时，由 EIP 编辑者合并到 EIP 存储库中。

**审查** - EIP 作者将 EIP 标记为准备好并请求同行评审。

**最后呼叫** - 这是 EIP 在转为 `最终` 之前的最后审查窗口。EIP 编辑者将分配 `最后呼叫` 状态并设置审查结束日期（`last-call-deadline`），通常为 14 天后。

如果此期间需要进行必要的规范性更改，则将 EIP 恢复为 `审查`。

**最终** - 此 EIP 代表最终标准。最终 EIP 处于最终状态，仅应更新以纠正勘误和添加非规范性澄清。

将 EIP 从最后呼叫转为最终的 PR 应该不包含除状态更新以外的任何更改。任何内容或编辑提议的更改应与此状态更新 PR 分开，并在其之前提交。

**停滞** - 任何在 `草稿`、`审查` 或 `最后呼叫` 状态下，如果在 6 个月或更长时间内不活跃，则将其移至 `停滞`。EIP 可以通过作者或 EIP 编辑者将其恢复到 `草稿` 或其早期状态。如果未恢复，提案可能会永远保持在此状态。

*EIP 作者会被通知其 EIP 状态的任何算法更改*

**撤回** - EIP 作者已撤回提议的 EIP。此状态具有最终性，无法使用此 EIP 编号恢复。如果该想法在后期被追求，则视为新提案。

**持续** - 一种特殊状态，适用于旨在持续更新且不达到最终状态的 EIP。最显著的包括 EIP-1。

## 成功的 EIP 应包含什么？

每个 EIP 应包含以下部分：

- 前言 - RFC 822 风格的头部，包含有关 EIP 的元数据，包括 EIP 编号、简短描述性标题（最多 44 个字符）、描述（最多 140 个字符）和作者详细信息。无论类别如何，标题和描述都不应包含 EIP 编号。有关详细信息，请参见 [下文](./eip-1.md#eip-header-preamble)。
- 摘要 - 摘要是一个多句（短段落）技术总结。这应该是规范部分的非常简洁且易于理解的版本。有人应该能够仅通过阅读摘要来了解该规范的主要内容。
- 动机 *(可选)* - 动机部分对于希望更改以太坊协议的 EIP 至关重要。它应清楚地解释现有协议规范为何不足以解决 EIP 所解决的问题。如果动机显而易见，则可以省略此部分。
- 规范 - 技术规范应描述任何新特性的语法和语义。规范应详细到足以允许当前以太坊平台（如 besu、erigon、ethereumjs、go-ethereum、nethermind 或其他）之间的竞争性、互操作性实现。
- 理由 - 理由通过描述设计动机和为何做出特定设计决策来充实规范。它应描述考虑过的替代设计和相关工作，例如该特性在其他语言中的支持。理由应讨论在 EIP 讨论中提出的重要异议或关注。
- 向后兼容性 *(可选)* - 所有引入向后不兼容性的 EIP 必须包含描述这些不兼容性及其后果的部分。EIP 必须解释作者打算如何处理这些不兼容性。如果提案不引入任何向后不兼容性，则可以省略此部分，但如果存在向后不兼容性，则必须包含此部分。
- 测试用例 *(可选)* - 对于影响共识更改的 EIP，实施的测试用例是强制性的。测试应作为数据（例如输入/预期输出对）内联在 EIP 中，或包含在 `../assets/eip-###/<filename>` 中。对于非核心提案，可以省略此部分。
- 参考实现 *(可选)* - 一个可选部分，包含参考/示例实现，供人们使用以帮助理解或实现此规范。对于所有 EIP，可以省略此部分。
- 安全考虑 - 所有 EIP 必须包含讨论与提议更改相关的安全影响/考虑的部分。包括可能对安全讨论重要的信息，表面风险，并可在提案的整个生命周期中使用。例如，包括与安全相关的设计决策、关注、重要讨论、特定于实现的指导和陷阱、威胁和风险的概述以及如何应对这些问题。缺少“安全考虑”部分的 EIP 将被拒绝。没有经过审阅者认为足够的安全考虑讨论，EIP 不能进入“最终”状态。
- 版权豁免 - 所有 EIP 必须属于公共领域。版权豁免必须链接到许可证文件，并使用以下措辞：`Copyright and related rights waived via [CC0](../LICENSE.md).`

## EIP 格式和模板

EIP 应以 [markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) 格式编写。可以遵循 [模板](https://github.com/ethereum/EIPs/blob/master/eip-template.md)。

## EIP 头部前言

每个 EIP 必须以 [RFC 822](https://www.ietf.org/rfc/rfc822.txt) 风格的头部前言开始，前后各有三个短横线（`---`）。此头部也被称为 Jekyll 的“前置内容”（"front matter"）。头部必须按以下顺序出现。

`eip`: *EIP 编号*

`title`: *EIP 标题是几个词，而不是完整的句子*

`description`: *描述是一个完整的（简短）句子*

`author`: *作者或作者的姓名和/或用户名，或姓名和电子邮件的列表。详细信息如下。*

`discussions-to`: *指向官方讨论线程的 URL*

`status`: *草稿、审查、最后呼叫、最终、停滞、撤回、持续*

`last-call-deadline`: *最后呼叫期结束的日期*（可选字段，仅在状态为 `最后呼叫` 时需要）

`type`: *`标准轨道`、`元` 或 `信息性` 之一*

`category`: *`核心`、`网络`、`接口` 或 `ERC` 之一*（可选字段，仅在 `标准轨道` EIP 时需要）

`created`: *EIP 创建日期*

`requires`: *EIP 编号*（可选字段）

`withdrawal-reason`: *解释 EIP 撤回原因的句子*（可选字段，仅在状态为 `撤回` 时需要）

允许列表的头部必须用逗号分隔元素。

要求日期的头部将始终采用 ISO 8601 格式（yyyy-mm-dd）。

### `author` 头部
`author` 头部列出了 EIP 的作者/拥有者的姓名、电子邮件地址或用户名。那些希望保持匿名的人可以仅使用用户名，或使用名字和用户名。`author` 头部值的格式必须是：

> Random J. User &lt;address@dom.ain&gt;

或

> Random J. User (@username)

或

> Random J. User (@username) &lt;address@dom.ain&gt;

如果包含电子邮件地址和/或 GitHub 用户名，并且

> Random J. User

如果既没有提供电子邮件地址也没有提供 GitHub 用户名。

至少一个作者必须使用 GitHub 用户名，以便在变更请求时获得通知并有能力批准或拒绝它们。

### `discussions-to` 头部

当 EIP 处于草案状态时，`discussions-to` 头部将指示 EIP 正在讨论的 URL。

首选的讨论 URL 是 [Ethereum Magicians](https://ethereum-magicians.org/) 上的主题。该 URL 不能指向 GitHub 拉取请求、任何短暂的 URL，以及任何可能随着时间被锁定的 URL（即 Reddit 主题）。

### `type` 头部

`type` 头部指定 EIP 的类型：标准跟踪、元或信息性。如果跟踪是标准，请包括子类别（核心、网络、接口或 ERC）。

### `category` 头部

`category` 头部指定 EIP 的类别。这仅对标准跟踪 EIP 是必需的。

### `created` 头部

`created` 头部记录 EIP 被分配编号的日期。两个头部应采用 yyyy-mm-dd 格式，例如 2001-08-14。

### `requires` 头部

EIP 可能有一个 `requires` 头部，指示该 EIP 依赖的 EIP 编号。如果存在这样的依赖关系，则该字段是必需的。

当当前 EIP 无法理解或实现而不依赖于另一个 EIP 的概念或技术元素时，会创建 `requires` 依赖关系。仅仅提到另一个 EIP 并不一定会创建这样的依赖关系。

## 链接到外部资源

除了下面列出的特定例外外，**不应**包含指向外部资源的链接。外部资源可能会意外消失、移动或更改。

有关允许的外部资源的过程在 [EIP-5757](./eip-5757.md) 中进行了描述。

### 执行客户端规范

可以使用正常的 markdown 语法包含指向以太坊执行客户端规范的链接，例如：

```markdown
[Ethereum Execution Client Specifications](https://github.com/ethereum/execution-specs/blob/9a1f22311f517401fed6c939a159b55600c454af/README.md)
```

这将呈现为：

[以太坊执行客户端规范](https://github.com/ethereum/execution-specs/blob/9a1f22311f517401fed6c939a159b55600c454af/README.md)

允许的执行客户端规范 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^(https://github.com/ethereum/execution-specs/(blob|commit)/[0-9a-f]{40}/.*|https://github.com/ethereum/execution-specs/tree/[0-9a-f]{40}/.*)$
```

### 执行规范测试

可以使用正常的 markdown 语法包含指向以太坊执行规范测试 (EEST) 的链接，例如：

```markdown
[Ethereum Execution Specification Tests](https://github.com/ethereum/execution-spec-tests/blob/c9b9307ff320c9bb0ecb9a951aeab0da4d9d1684/README.md)
```

这将呈现为：

[以太坊执行规范测试](https://github.com/ethereum/execution-spec-tests/blob/c9b9307ff320c9bb0ecb9a951aeab0da4d9d1684/README.md)

允许的执行规范测试 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式之一：

```regex
^https://(www\.)?github\.com/ethereum/execution-spec-tests/(blob|tree)/[a-f0-9]{40}/.+$
```

```regex
^https://(www\.)?github\.com/ethereum/execution-spec-tests/commit/[a-f0-9]{40}$
```

### 共识层规范

可以使用正常的 markdown 语法包含指向以太坊共识层规范中特定提交的文件的链接，例如：

```markdown
[Beacon Chain](https://github.com/ethereum/consensus-specs/blob/26695a9fdb747ecbe4f0bb9812fedbc402e5e18c/specs/sharding/beacon-chain.md)
```

这将呈现为：

[信标链](https://github.com/ethereum/consensus-specs/blob/26695a9fdb747ecbe4f0bb9812fedbc402e5e18c/specs/sharding/beacon-chain.md)

允许的共识层规范 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^https://github.com/ethereum/consensus-specs/(blob|commit)/[0-9a-f]{40}/.*$
```

### 网络规范

可以使用正常的 markdown 语法包含指向以太坊网络规范中特定提交的文件的链接，例如：

```markdown
[Ethereum Wire Protocol](https://github.com/ethereum/devp2p/blob/40ab248bf7e017e83cc9812a4e048446709623e8/caps/eth.md)
```

这将呈现为：

[以太坊网络协议](https://github.com/ethereum/devp2p/blob/40ab248bf7e017e83cc9812a4e048446709623e8/caps/eth.md)

允许的网络规范 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^https://github.com/ethereum/devp2p/(blob|commit)/[0-9a-f]{40}/.*$
```

### 门户规范

可以使用正常的 markdown 语法包含指向以太坊门户规范中特定提交的文件的链接，例如：

```markdown
[Portal Wire Protocol](https://github.com/ethereum/portal-network-specs/blob/5e321567b67bded7527355be714993c24371de1a/portal-wire-protocol.md)
```

这将呈现为：

[门户网络协议](https://github.com/ethereum/portal-network-specs/blob/5e321567b67bded7527355be714993c24371de1a/portal-wire-protocol.md)

允许的网络规范 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^https://github.com/ethereum/portal-network-specs/(blob|commit)/[0-9a-f]{40}/.*$
```

### 万维网联盟 (W3C)

可以使用正常的 markdown 语法包含指向 W3C "推荐" 状态规范的链接。例如，以下链接将被允许：

```markdown
[Secure Contexts](https://www.w3.org/TR/2021/CRD-secure-contexts-20210918/)
```

这将呈现为：

[安全上下文](https://www.w3.org/TR/2021/CRD-secure-contexts-20210918/)

允许的 W3C 推荐 URL 必须锚定到技术报告命名空间中的规范，并且必须匹配以下正则表达式：

```regex
^https://www\.w3\.org/TR/[0-9][0-9][0-9][0-9]/.*$
```

### Web 超文本应用技术工作组 (WHATWG)

可以使用正常的 markdown 语法包含指向 WHATWG 规范的链接，例如：

```markdown
[HTML](https://html.spec.whatwg.org/commit-snapshots/578def68a9735a1e36610a6789245ddfc13d24e0/)
```

这将呈现为：

[HTML](https://html.spec.whatwg.org/commit-snapshots/578def68a9735a1e36610a6789245ddfc13d24e0/)

允许的 WHATWG 规范 URL 必须锚定到 `spec` 子域中定义的规范（不允许想法规范）并且必须是提交快照，因此必须匹配以下正则表达式：

```regex
^https:\/\/[a-z]*\.spec\.whatwg\.org/commit-snapshots/[0-9a-f]{40}/$
```

尽管 WHATWG 不推荐，EIP 必须锚定到特定的提交，以便未来的读者可以参考 EIP 最终确定时存在的活标准的确切版本。这为读者提供了足够的信息，以便在选择时与 EIP 引用的版本和当前活标准保持兼容。

### 互联网工程任务组 (IETF)

可以使用正常的 markdown 语法包含指向 IETF 请求评论 (RFC) 规范的链接，例如：

```markdown
[RFC 8446](https://www.rfc-editor.org/rfc/rfc8446)
```

这将呈现为：

[RFC 8446](https://www.rfc-editor.org/rfc/rfc8446)

允许的 IETF 规范 URL 必须锚定到具有分配的 RFC 编号的规范（这意味着不能引用互联网草案），并且必须匹配以下正则表达式：

```regex
^https:\/\/www.rfc-editor.org\/rfc\/.*$
```

### 比特币改进提案

可以使用正常的 markdown 语法包含指向比特币改进提案的链接，例如：

```markdown
[BIP 38](https://github.com/bitcoin/bips/blob/3db736243cd01389a4dfd98738204df1856dc5b9/bip-0038.mediawiki)
```

这将呈现为：

[BIP 38](https://github.com/bitcoin/bips/blob/3db736243cd01389a4dfd98738204df1856dc5b9/bip-0038.mediawiki)

允许的比特币改进提案 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^(https://github.com/bitcoin/bips/blob/[0-9a-f]{40}/bip-[0-9]+\.mediawiki)$
```

### 国家漏洞数据库 (NVD)

可以包含指向国家标准与技术研究所 (NIST) 发布的常见漏洞和暴露 (CVE) 系统的链接，前提是它们由最近更改的日期进行限定，使用以下语法：
```markdown
[CVE-2023-29638 (2023-10-17T10:14:15)](https://nvd.nist.gov/vuln/detail/CVE-2023-29638)
```

渲染为：

[CVE-2023-29638 (2023-10-17T10:14:15)](https://nvd.nist.gov/vuln/detail/CVE-2023-29638)

### 链无关改进提案 (CAIPs)

可以使用正常的 Markdown 语法包含指向链无关改进提案 (CAIPs) 规范的链接，例如：

```markdown
[CAIP 10](https://github.com/ChainAgnostic/CAIPs/blob/5dd3a2f541d399a82bb32590b52ca4340b09f08b/CAIPs/caip-10.md)
```

渲染为：

[CAIP 10](https://github.com/ChainAgnostic/CAIPs/blob/5dd3a2f541d399a82bb32590b52ca4340b09f08b/CAIPs/caip-10.md)

允许的链无关 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^(https://github.com/ChainAgnostic/CAIPs/blob/[0-9a-f]{40}/CAIPs/caip-[0-9]+\.md)$
```

### 以太坊黄皮书

可以使用正常的 Markdown 语法包含指向以太坊黄皮书的链接，例如：

```markdown
[Ethereum Yellow Paper](https://github.com/ethereum/yellowpaper/blob/9c601d6a58c44928d4f2b837c0350cec9d9259ed/paper.pdf)
```

渲染为：

[以太坊黄皮书](https://github.com/ethereum/yellowpaper/blob/9c601d6a58c44928d4f2b837c0350cec9d9259ed/paper.pdf)

允许的黄皮书 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^(https://github\.com/ethereum/yellowpaper/blob/[0-9a-f]{40}/paper\.pdf)$
```

### 执行客户端规范测试

可以使用正常的 Markdown 语法包含指向以太坊执行客户端规范测试的链接，例如：

```markdown
[Ethereum Execution Client Specification Tests](https://github.com/ethereum/execution-spec-tests/blob/d5a3188f122912e137aa2e21ed2a1403e806e424/README.md)
```

渲染为：

[以太坊执行客户端规范测试](https://github.com/ethereum/execution-spec-tests/blob/d5a3188f122912e137aa2e21ed2a1403e806e424/README.md)

允许的执行客户端规范测试 URL 必须锚定到特定的提交，因此必须匹配以下正则表达式：

```regex
^(https://github.com/ethereum/execution-spec-tests/(blob|commit)/[0-9a-f]{40}/.*|https://github.com/ethereum/execution-spec-tests/tree/[0-9a-f]{40}/.*)$
```

### 数字对象标识符系统

可以使用以下语法包含带有数字对象标识符 (DOI) 的链接：

````markdown
This is a sentence with a footnote.[^1]

[^1]:
    ```csl-json
    {
      "type": "article",
      "id": 1,
      "author": [
        {
          "family": "Jameson",
          "given": "Hudson"
        }
      ],
      "DOI": "00.0000/a00000-000-0000-y",
      "title": "An Interesting Article",
      "original-date": {
        "date-parts": [
          [2022, 12, 31]
        ]
      },
      "URL": "https://sly-hub.invalid/00.0000/a00000-000-0000-y",
      "custom": {
        "additional-urls": [
          "https://example.com/an-interesting-article.pdf"
        ]
      }
    }
    ```
````

渲染为：

<!-- markdownlint-capture -->
<!-- markdownlint-disable code-block-style -->

这是带有脚注的句子。[^1]

[^1]:
    ```csl-json
    {
      "type": "article",
      "id": 1,
      "author": [
        {
          "family": "Jameson",
          "given": "Hudson"
        }
      ],
      "DOI": "00.0000/a00000-000-0000-y",
      "title": "An Interesting Article",
      "original-date": {
        "date-parts": [
          [2022, 12, 31]
        ]
      },
      "URL": "https://sly-hub.invalid/00.0000/a00000-000-0000-y",
      "custom": {
        "additional-urls": [
          "https://example.com/an-interesting-article.pdf"
        ]
      }
    }
    ```

<!-- markdownlint-restore -->

请参阅 [引用样式语言架构](https://resource.citationstyles.org/schema/v1.0/input/json/csl-data.json) 以获取支持的字段。除了通过该架构的验证外，引用必须包含 DOI 和至少一个 URL。

顶级 URL 字段必须解析为可以零成本查看的引用文档的副本。`additional-urls` 下的值也必须解析为引用文档的副本，但可以收取费用。

## 链接到其他 EIP

对其他 EIP 的引用应遵循格式 `EIP-N`，其中 `N` 是您所指的 EIP 编号。每个在 EIP 中引用的 EIP **必须**在首次引用时附带相对 Markdown 链接，并且 **可以**在后续引用中附带链接。链接 **必须** 始终通过相对路径进行，以便链接在此 GitHub 存储库、该存储库的分支、主 EIP 网站、主 EIP 网站的镜像等中有效。例如，您可以将此 EIP 链接为 `./eip-1.md`。

## 辅助文件

图像、图表和辅助文件应包含在该 EIP 的 `assets` 文件夹的子目录中，格式为：`assets/eip-N`（其中 **N** 替换为 EIP 编号）。在 EIP 中链接图像时，请使用相对链接，例如 `../assets/eip-1/image.png`。

## 转移 EIP 所有权

有时需要将 EIP 的所有权转移给新的负责人。一般来说，我们希望保留原作者作为转移 EIP 的共同作者，但这实际上取决于原作者。转移所有权的一个好理由是原作者不再有时间或兴趣更新它或跟进 EIP 过程，或者已经失联（即无法联系或未回复电子邮件）。转移所有权的一个坏理由是因为您不同意 EIP 的方向。我们尝试围绕 EIP 建立共识，但如果这不可能，您可以随时提交一个竞争的 EIP。

如果您有兴趣承担 EIP 的所有权，请发送一条消息请求接管，地址发送给原作者和 EIP 编辑。如果原作者未能及时回复电子邮件，EIP 编辑将做出单方面决定（这样的决定并不是不能被撤销的 :））。

## EIP 编辑

当前的 EIP 编辑是

- Alex Beregszaszi (@axic)
- Greg Colvin (@gcolvin)
- Matt Garnett (@lightclient)
- Sam Wilson (@SamWilsn)
- Zainan Victor Zhou (@xinbenlv)
- Gajinder Singh (@g11tech)

名誉 EIP 编辑是

- Casey Detrio (@cdetrio)
- Gavin John (@Pandapip1)
- Hudson Jameson (@Souptacular)
- Martin Becze (@wanderer)
- Micah Zoltu (@MicahZoltu)
- Nick Johnson (@arachnid)
- Nick Savers (@nicksavers)
- Vitalik Buterin (@vbuterin)

如果您想成为 EIP 编辑，请查看 [EIP-5069](./eip-5069.md)。

## EIP 编辑职责

对于每个新提交的 EIP，编辑会执行以下操作：

- 阅读 EIP 以检查其是否准备就绪：合理且完整。即使这些想法似乎不太可能达到最终状态，它们也必须在技术上有意义。
- 标题应准确描述内容。
- 检查 EIP 的语言（拼写、语法、句子结构等）、标记（GitHub 风格的 Markdown）、代码风格

如果 EIP 不准备好，编辑将将其退回给作者进行修订，并附上具体说明。

一旦 EIP 准备好进入存储库，EIP 编辑将：

- 分配一个 EIP 编号（通常是递增的；如果怀疑存在编号抢注，编辑可以重新分配）
- 合并相应的 [拉取请求](https://github.com/ethereum/EIPs/pulls)
- 向 EIP 作者发送消息，告知下一步。

许多 EIP 是由具有以太坊代码库写入权限的开发人员编写和维护的。EIP 编辑监控 EIP 更改，并纠正我们看到的任何结构、语法、拼写或标记错误。

编辑不对 EIP 进行评判。我们只是执行行政和编辑部分。

## 风格指南

### 标题

前言中的 `title` 字段：

- 不应包含“标准”一词或其任何变体；并且
- 不应包含 EIP 的编号。

### 描述

前言中的 `description` 字段：

- 不应包含“标准”一词或其任何变体；并且
- 不应包含 EIP 的编号。

### EIP 编号

当提到 `category` 为 `ERC` 的 EIP 时，必须以连字符形式书写 `ERC-X`，其中 `X` 是该 EIP 的分配编号。当提到其他 `category` 的 EIP 时，必须以连字符形式书写 `EIP-X`，其中 `X` 是该 EIP 的分配编号。

### RFC 2119 和 RFC 8174

鼓励 EIP 遵循 [RFC 2119](https://www.ietf.org/rfc/rfc2119.html) 和 [RFC 8174](https://www.ietf.org/rfc/rfc8174.html) 的术语，并在规范部分的开头插入以下内容：
> 本文档中的关键字 "MUST"、"MUST NOT"、"REQUIRED"、"SHALL"、"SHALL NOT"、"SHOULD"、"SHOULD NOT"、"RECOMMENDED"、"NOT RECOMMENDED"、"MAY" 和 "OPTIONAL" 应按照 RFC 2119 和 RFC 8174 中的描述进行解释。

## 历史

本文件主要来源于 [Bitcoin's BIP-0001](https://github.com/bitcoin/bips)，由 Amir Taaki 编写，而该文档又源自 [Python's PEP-0001](https://peps.python.org/)。在许多地方，文本被简单复制并修改。尽管 PEP-0001 的文本是由 Barry Warsaw、Jeremy Hylton 和 David Goodger 编写的，但他们对其在以太坊改进过程中的使用不负责任，也不应被打扰以太坊或 EIP 相关的技术问题。请将所有评论直接发送给 EIP 编辑。

## 版权

通过 [CC0](../LICENSE.md) 放弃版权及相关权利。