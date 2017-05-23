---
permalink: /architecture
layout: documentation
section-type: documentation
title: Dragonchain Architecture
sitemap:
priority: 1.0
---

<h4 class="no_toc">Version 3 - November 2016</h4>

<h5 class="no_toc">[DRAFT]</h5>

Joe Roets - j03 - joe@dragonchain.org

<h5 class="no_toc">[TRANSLATOR]</h5>

金快快 - jinkuaikuai@yeah.net

---

<h2 class="no_toc">Table of Contents</h2>

* TOC
{:toc}

---

# 龙链架构

该文档概述和传达了龙链平台的整体架构和设计方案，以便第三方业务应用更加轻松地接入龙链技术。在作者看来，接入简单的区块链技术是一个不断增长的需求。龙链的私有链的奇妙实现方式满足了那些想在商业区块链应用中保护信息和控制业务流程的需求。该文档寻求阐明龙链架构的一些不错成就和例子。



## 架构目标

1. 易于集成现有系统
1. 对于不熟悉区块链、分布式系统和密码学的传统工程师和程序员来说，容易上手
1. 客户端和服务端交互完全基于RESTful协议。
1. 简单的体系结构(灵活且可用于不可预见的应用)
1. 默认为商业数据提供保护服务
1. 允许更集中于业务流程的控制
1. 固定一段时间区块的长度
1. 更短和更快的区块
1. 不定货币区块链(多货币支持)
1. 无基础货币
1. 与公有链和私有链的互操作性
1. 标准采用 [see W3C Blockchain Community Group blockchain standardization](https://github.com/w3c/blockchain/blob/master/standards.md) 和 [Disney Blockchain Standardization Notes](https://dragonchain.github.io/blockchain-standardization)


## 架构组成

### 证明概念

在比特币和其他加密货币，我们一般使用“工作量证明”(PoW)共识算法。龙链抽象了出了“证明”，支持多种共识证明算法的实现。某些用户想使用完全基于信任的系统，例如在一个私有链中。另外也能在一个混合的证明配置中发现“工作量证明”在抵御黑客攻击的价值。(例如,一个潜在的攻击者打算获取一组私钥，但是他需要额外消耗大量的计算来实现共识证明，以完成对区块的收集。)

在比特币和其他大多数加密货币中，我们见证了“工作证明”(PoW)算法的使用，作为在“信任”系统中达成共识的基础。在这个体系结构中，“证明”将被抽象出来，并可以用一个或多个方法实现给定的区块链。对于一些使用，人们可能希望使用基于信任的系统，例如，在一个完全私有的区块链系统中。一个混合的证明中,为了抵御攻击，添加额外的安全工作(即潜在的攻击者不仅需要妥协或获得一组私钥,但也需要执行额外的计算完成给定区块链收集的证据。）

工作量证明实现:

1. 信任模式 (默认)
1. 工作量证明 (PoW)
1. 股权证明 (PoS)
1. 其他一些共识证明算法



考虑到这种抽象，用户将能够配置一个或多个同步的共识证明来适应业务需求，而系统开发人员可能会构建新的证明实现，因为区块链技术也在进步。

即使是在固定时间长度块结构的情况下也可以进行PoW(参见本文中其他地方的块构造讨论)，通过跨越块来跨越一个或多个共识证明实现。举个例子，假设我们有一个特定的用例需要高于正常的安全性。如果我们假设信任是默认实现的，但是我们希望配置一些可信的验证，我们可能希望在区块链上配置一些工作证明。根据所配置的难度级别，并给出了PoW算法的性质，我们可能不会看到每个块的PoW解决方案。一些区块将没有PoW，而PoW可能只是偶尔出现。在这种情况下，可以合理配置两个或更多的PoW级别。一个更高难度的证明可能大约每20分钟出现一次，并且一个较低难度的证明可能大约每2秒出现一次。同样，其他的证明，如PoS，也可以同时应用于单个链中。一个有趣的哲学观点是，这样的证据可能被用于对抗未来的攻击者，而不是与其他矿工竞争以获得一个块的奖励。

#### 校验和存在证明

抽象的另一个元素是通过对其他(公共)区块链的检查来进行进一步"杂交"的能力。这可以被看作是第一级或者是区块链、公共或私有之间的简单互操作性。特别的潜在价值，是通过测量公共区块链的属性来确定风险的能力。也就是说，如果与使用PoW(如比特币)的区块链捆绑在一起，系统可以估计自检查点以来已经应用的hashpower的数量，甚至可以推断计算能力用于花费的金额。有了这个，就可以开发出一个风险单元，它可以显示出需要多少计算能力(以及需要多少成本)来计算成功的可能性，以试图伪造一个给定的东西(例如，一个高价值的交易)。以同样的方式，将一个检查点与基于PoS的公共区块链捆绑在一起，系统可以度量必须持有(并且可能是献出)的资产的数量，以伪造交易。有关更多信息，请参阅下面的第5级验证。

## Transaction定义

一个Transaction是所有事件和数据传输记录在区块链上的基础。系统应该定义灵活且可扩展的标准化Transaction结构。
备选方案:

- JSON (标准结构)
- JWT (JSON Web Token)
- 其他编码结构 (支持多种语言的库)

### Header

包含transaction的系统和网络的一些标准字段。
举例字段:

- `Transaction ID`
- `Transaction type`
- `Transaction class`
- `Create timestamp`
- `Transaction timestamp`
- `Origin ID`
- `Business organization and/or organization taxonomy`
- `Actor`
- `Entity`

### Payload

在业务层次上定义的任意结构和内容，并在approval(level 1)层代码中实现或控制。
在有效payload(例如字段和结构)内的某种级别的结构可以被实现为网络的模板，根据可选的“transaction class”字段来使用和控制。
他将允许节点实现企业或网络级别上定义的一些需要的行为，以及简化诸如货币之类的功能。参见下面的事务类示例。

### Signature

transaction的一部分持有一个密码签名，以允许双方证明transaction的来源和/或交易的内容自签名后没有改变(例如篡改证据)。transaction中的签名不应该来自transaction来源，例如有些客户端没有签名的能力或者没有意识到后续的区块链。或者，一个客户端系统可以在transaction提交之前调用多重签名。无论如何，区块链平台端的Transaction服务组件应该加密任何到达接受处理的ransaction。
感知到的要求:

-该结构应该允许多重和嵌套签名
-该签名应该在签名结构本身的所有字段中散列，以使签名本身不容易被篡改
-该结构应该在记录验证(块验证)签名过程中重新使用

字段:

- `Signatory`
- `Hash`
- `Stripped Hash` _(for transaction signatures only - a hash of the full transaction with the payload stripped - to allow level 2+ nodes to validate a transaction even when the payload is not available)_
- `Public Key`
- `Signing Timestamp`
- `Signature`
- `Child Signature` _(optional)_

实现选项:

- JWS (JSON Web Signature)
- Custom JSON

### Classes

一些可能的transaction Classes的示例:

- `Default` _(custom Level 1 business payload structure)_
- `Currency`
- `Provisioning`
- `Network communications`
- `Marketplace transaction`
- `Information interoperability` _(foreign blockchain currency or information payload)_


## 块定义

块定义可能会看到多个实现，尽管有更多或更少的常见元素，例如

- `Block ID`
- `Timestamp`
- `Transactions`
- `Hash of prior block`
- `Proof` _(e.g. PoW, PoS) artifact_
- `Signature`
- `Block period`
- `Verification attributes`

块定义设计中通常不考虑的一个有趣的部分是这个块何时形成的问题。在比特币或其他PoW系统中，当PoW算法需要解决目前的网络难度时，就会形成一个块。这可能在30秒或30分钟后发生。它是可变和随机的，但是网络会试图调整难度，以调整平均块时间到10分钟。

## 验证和共识

我们在这里引入了“基于上下文的验证”的概念，以进行区块链的讨论。 可以考虑比特币或其他现有的区块链实现。他们主要使用一种证明算法(例如PoW)来组装区块，并达成一致意见，即哪些块和哪个链是通用的和公认的。

![Vanilla Blockchain Structure is One Dimensional](doc/img/vanilla-1d-blockchain-verification.png "Vanilla Blockchain Structure is One Dimensional")

<div class="caption">Vanilla Blockchain Structure is One Dimensional</div>

在龙链中，通过基于上下文的核准，我们为该设计添加了另一个维度。因此，第一级是在纯业务上下文中实现的，这是用业务逻辑实现的，提供了对transaction核准和一些其他系统逻辑，以便将这些transaction安排到被链接在一起的块中。这些块将具有一个抽象的共识算法集成，如PoW、PoS或Trust。第一级的验证可以被视为类似于其他区块链。

当添加其他验证上下文时，我们会看到被添加的上下文甚至是基于信任的系统。

任何给定的节点都应该允许配置支持或执行下面描述的一个或多个验证阶段。
![Context Based Blockchain Verification](doc/img/context-based-blockchain-verification.png "Context Based Blockchain Verification")

<div class="caption">Dragonchain Structure is Linked Between Blocks and Verification Contexts</div>


#### Level 1 - 业务(Approval)验证

5级节点将为一个或多个公共块链提供桥梁，并允许客户端与其进行交互（例如比特币，以太坊，莱特币等）。

这将提供一个重要的功能是检查点，或在公共块链上放置“存在证明”的数据的散列。对于检查点操作，5级节点将接受transaction，任何级别的块验证，任意字符串或任意散列。该参数将被散列，并将该哈希添加到放在公共块链上的transaction中。这个散列的存在可以用来证明这个数据是存在的，在一定的状态下使用公共的块链数据。组织可以使用这种证明来测量和减轻风险，这是基于从那时以来耗费的全网算力的估计或计算（在工作块证明的情况下）。例如，一个200万美元的交易可能会传递给5级节点，以便尽快放置在比特币块上，而在某个时间点，一方可以使用该信息作为来源来衡量散列数量自那时以来已经消耗了，计算攻击者可以成功地假冒比特币块给出特定百分比的全网算力的可能性，并推断或估计耗费这个算力的成本（硬件或货币的牺牲）。如果这一过程导致对业务满意的风险评估，则可以信任并接受交易。

公共桥梁功能的另一个重要方面是在私人和公共方面跟踪资产的能力。也就是说，考虑到使用比特币地址实现的内部货币（请参阅本文档中的货币部分），可以使用公共API或服务在比特币上发出令牌，并且该令牌可能同时存在于私有块链和比特币公开块中。密钥或钱包的所有者将能够使用公共或私有块链互动来传送令牌或资产。 5级节点可用于在块链之间跟踪该资产，并使它们保持彼此同步。

#### Level 2 - Enterprise (Validation) Verification

This context is defined Enterprise or network wide, and checks for block and individual transaction validity in form, signature, and required data elements.

Verified elements:

1. Block (verification record) construction and signature
1. Individual transaction signatures
1. Individual transaction header elements (that all required header fields are present)

A Level 2 node will assemble a new verification record which will contain:

1. A list of valid transactions and a list of invalid transaction, and in this manner vote on the validity of individual transactions.
1. The hash of the prior Level 2 record created by this node for the same origin (Level 1) node (thus creating a Level 2 blockchain)
1. The hash of the Level 1 block which was validated (thus providing a second dimension to the blockchain)
1. Node owner identity information
1. Node deploy location (data center)
1. Node key management authority information

#### Level 3 - Network Diversity Verification

Defined enterprise wide, a Level 3 node will verify diversity of validation (Level 2) verifications. That is, a Level 3 node will check the following criteria:

1. Count of Level 2 verification records have been received
1. That those records have come from (configurable count) of unique business units
1. That those records have come from (configurable count) of unique deployment locations
1. That those records have come from (configurable count) of unique key management authorities

This verification context will ensure that validations of transactions are coming from a sufficiently diverse set of distributed sources. It also provides control and measurement of network effect and provides distributed security as an attacker would be required to attack multiple systems, businesses, and data centers in order to tamper with existing data.

A Level 3 node will assemble a new verification record containing:

1. Remnants of criteria met (e.g. Level 2 verification record count, set of business units, set of data centers).
1. The hash of the prior Level 3 record created by this node for the same origin (Level 1) node (thus creating a Level 3 blockchain)
1. The hash of the Level 2 verification records which passed the criteria (thus providing a second dimension to the blockchain)

#### Level 4 - External Partner (Notary) Verification

Defined network wide (Enterprise+), a level 4 node will provide a notary functionality to the consensus process. Hosted by an external partner, a level 4 node would cryptographically sign any level 3 verification records that it receives. This function allows the Level 4 node to act as an independent witness to level 3 verifications.

#### Level 5 - 公有链检查点

5级节点将为一个或多个公共块链提供桥梁，并允许客户端与其进行交互（例如比特币，以太坊，莱特币等）。

这将提供一个重要的功能是检查点，或在公共块链上放置“存在证明”的数据的散列。对于检查点操作，5级节点将接受交易数据，任何级别的块验证数据，任意字符串或任意散列数据。该参数将被哈希，并将该哈希值添加到放在公共块链上的交易中。这个哈希的存在可以用来证明这个数据是存在的，在一定的状态下使用公共的块链数据。组织可以使用这种证明来测量和减轻风险，这是基于从那时以来耗费的算力的估计或计算（在工作块证明的情况下）。例如，一个200万美元的交易可能会传递给5级节点，以便尽快放置在比特币块上，而在某个时间点，一方可以使用该信息作为来源来衡量散列数量自那时以来已经消耗了，计算攻击者可以成功地假冒比特币块给出特定百分比的全局散列强度的可能性，并推断或估计耗费这个算力的成本（以及硬件和/或货币的牺牲）。如果这一过程导致对业务满意的风险评估，则可以信任并接受交易。

公共桥梁功能的另一个重要方面是在私人和公共方面跟踪资产的能力。也就是说，考虑到使用比特币地址实现的内部货币（请参阅本文档中的货币部分），可以使用公共API或服务在比特币上发出令牌，并且该令牌可能同时存在于私有块链和比特币公开块中。密钥或钱包的所有者将能够使用公共或私有块链互动来传送令牌或资产。 5级节点可用于在块链之间跟踪该资产，并使它们保持彼此同步。

#### Level X - 专有的上下文验证

业务、企业或整个网络都应该能够定义自定义验证上下文，并执行它以满足业务需求。
可以配置一个节点来运行任意的验证上下文，这将以下列方式触发:
1。通过接收来自另一个节点的广播(连续或非连续的阶段)
1。定时或定时的触发器(如cron)
1。通过观察者模式通知

### 区块链的区块链 概念

这种架构最好被理解为“区块链的区块链”。也就是说，一个业务审批函数节点(参见下面的第1级)在一个一维的级别上是一个标准的区块链。但是，每个业务关注点通常都有自己的节点来完成这项工作，每个都有自己的区块链。正是在这里，这些区块链形成了共识。

![Blockchain of Blockchains](doc/img/blockchain-of-blockchains.png "Blockchain of Blockchains")

<div class="caption">Blockchain of Blockchains</div>

## 货币

该架构允许用户对货币进行建模，并在设计后期获利。 用户可以将来自多个源的信息放在块链上方，随时间观察其使用情况，并根据业务和客户的优先级确定其价值。 在这一点上，资产和活动可以获利，并且可以开发采矿或铸造算法，这将激励企业的员工，团队或客户。 量化，货币化和激励的过程可能是无休止的调整，但将提供透明的经济制度。
该体系结构的实现应该为便于部署提供一个或多个模板或可配置的货币。这样的实现可以定义诸如挖矿或铸币算法、寻址、钱包管理等等，用户还应该为进一步的实验和定制提供可扩展的功能。

### 货币模型

该体系结构允许用户在设计中对货币进行建模，并将其货币化。用户可以从区块链上的许多源处放置信息，观察其随时间的使用，并根据业务和客户的优先级确定其价值。在这一点上，资产和活动可以被货币化，可以开发一种挖矿算法，这将激励企业的员工、团队或客户。这个量化、货币化和激励的过程可能是无止境的调整，但将提供一个透明的经济体系。
这个框架有可能在组织中提供敏捷性，在这些组织中，数据提供者能够立即及早地获得研究数据，报告和其他信息，而这些信息将不会另外看到这样的数据，因为它通常需要自上而下 组织审批费用高昂，风险大。 可以跟踪使用这些数据并确定其价值，从而导致直接货币化和自下而上的融资机制。

### 比特币地址

基础实施应该可能默认（至少在可预见的未来）利用比特币寻址和加密技术，以利用不断增长的外部比特币生态系统。 例如，以私人货币使用比特币加密技术将能够透明地使用硬件签名钱包进行内部使用（例如KeepKey，Trezor，Ledger）。 另一个例子是令牌化，其中可以直接将比特币令牌提供技术与内部块链一起使用（例如Counterparty或Tokenly）。

### Interoperability

With the transaction class header field, it is possible to wrap foreign cryptocurrency transactions within a private blockchain transaction.

## Smart Contracts

Many questions arise in regards to blockchain based “smart contracts”...

#### Turing Completeness

Top of this list is Turing completeness. Bitcoin is purposely _not_ Turing complete. It is as complex as necessary to provide the capabilities needed for its application.

Some blockchain implementations such as Ethereum are Turing complete. This allows some very interesting applications, yet brings some risk and difficulty in implementation. A smart contract must be executed within a special and verified container or virtual machine to insure deterministic results no matter the hardware or operating system upon which the node is running. Various aspects of the smart contract must be monitored (e.g. loops) for failure or otherwise unexpected or unauthorized action.

#### Transactions, Atomicity, and Rollback

A smart contract system may need to provide some level of controlled or automated transaction and rollback capability to ensure that any given action will only take effect if the entire transaction operation is successfully completed.

#### Distributed Execution

Where is the smart contract executed? Is it on the distributed (calling) node?

### Dragonchain Smart Contracts

The architecture calls for definition of “approval context” code to be configured or deployed on Level 1 business nodes. This approval code can be considered a smart contract. By default, this smart contract will only be executed on that Level 1 node(s), and under the direct control of the business owner. This provides a familiar client/server interface to the creation of a smart contract, and simplifies the risk evaluation. The attack vectors are more common and known to modern engineers. Turing completeness is provided just as within any web service platform.

A smart contract may well be distributed, and may be executed on a pay per play basis. In this case, many of the risks with smart contracts described above apply, yet it can be assumed that the parties will have a trust relationship inside of an organization to draw on and the provisioning of the code may include necessary legal agreements.

In much the same way, a group may host smart contracts as a IT support business, and provide varying plans for chargeback/payment.

A smart contract may be employed on a node in the following manners:

- Hardcoded in a forked codebase
- Configured (with smart contract code deployed on system)
- Delivered via blockchain (admin would send multi-signed transaction with smart contract, start date, etc.)

## Subscription Data Feed

As transaction payloads are stripped prior to any broadcast within the consensus process, nodes will share business data as necessary via subscription data feeds. This amounts to a push or pull mechanism where some subset of a nodes transactions are continuously fed to another node.

In the case where a node needs another node’s data in order to “mash up” with its own data to serve a customer, that node would configure to request access from the “origin node” which owns the data. This request may come with authentication or authorization information and the origin node may approve or deny the request. The requesting node may also provide criteria for the data feed such as only a certain transaction type, only transactions that have surpassed a certain level of verification on the network, or only involving a certain identity. In this way, the requesting node will have a local cache of only the data that it needs, and it will be able to answer its customers with confidence that the data is unaltered and verified without the need to reach out to the origin business services in the future.

In the case where an origin node would like to distribute its data (i.e. for backup or disaster recovery), the node may pay for such a service and continuously push the data to that node. The backup node will be able to verify that the data is error free upon receipt and periodic audit.

## Network Management

For a blockchain network system, many typical elements will necessarily exist such as provisioning, discovery, maintenance of available and quality nodes, etc. The architecture will however take some philosophical positions on the configuration and maintenance of the network.

All considerations for network and communications management should in this architecture be made from the individual node context. Such decisions as which nodes to connect with should be decentralized decisions made by each node independently. There may well be mechanisms for central management of hints or starting lists of guaranteed available nodes within an Enterprise, but no attempt should be made to centralize the connection requirements of the network.

### Data distribution

By default, no transaction `payload` (business) data should leave an origin (owning) node nor be propagated across the network. As part of the consensus and network communications, all transaction payloads are to be stripped prior to broadcasting.

Only when explicitly authorized by the origin node are the payloads and full transactions caused to be broadcast to authorized nodes.

### Node Discovery

Node discovery should take place via peer to peer request.

### Network Marketplace

**IMPORTANT:** Any notion of **marketplace or currency** with which nodes may trade or hire out verifications **should not be implemented in the system infrastructure**, but **rather as an add-on currency implementation** on a node within the blockchain network. In this manner, the architecture will remain flexible and open to new ideas or unforeseen requirements.

### Node Quality Assessment

A node should track and ascertain the quality of connected and disconnected individual peers. Considering currently connected and disconnected peers separately, a node should track attributes such as:

- Average latency of the connection (periodically assessed)
- Signing success rate
- Rebroadcast counts
- Average verification time
- Deploy location (for diversity criteria)
- Owner (for diversity criteria)
- Unsuccessful connection attempts
- Last connection attempt
- Node success in gaining further levels of context based verification

A node should conduct periodic assessment of connected nodes more often than disconnected nodes, however over a reasonable span, the node should assess quality of disconnected nodes in order to have necessary intelligence to prioritize peer connections should a large portion of connected nodes become unavailable.

### Verification Receipt

A broadcast of a verification record to a higher level verification node should provide a receipt message notifying success or failure, and if successful, should include the verification record as signed by the higher level node. This same mechanism should be passed further down the line to reach the origin node. Although it is a design consideration, something in the manner of an asynchronous reply to another broadcast call may be an appropriate mechanism to provide such capability.

### Implementation Options

- Custom (e.g. Apache Thrift)
- RESTful service calls
- Distributed database framework with replication

## Interoperability and Proposed Standards

There are many avenues to consider for the question of interoperability with other blockchain systems.

### Checkpointing & Wrapping

One may choose to connect transactions or other artifacts via checkpointing (see Level 5 - Public Checkpoint above) or by wrapping a foreign transaction or artifact within a Dragonchain transaction (see foreign currency transaction header above).

### Subconsensus

A business may choose to employ another blockchain at any level of the verification process. For example, to provide a decentralized Level 1 approval implementation, one may choose to employ Bitcoin or other proof of work based blockchain to come to consensus on a currency transactions.

### Adoption

The architecture should be expected to adopt standards as they become available.

- [W3C Blockchain Community Group blockchain standardization](https://github.com/w3c/blockchain/blob/master/standards.md)
- [Disney Blockchain Standardization Notes](https://dragonchain.github.io/blockchain-standardization)

---

###### Dragonchain Architecture - Copyright © 2016 Joe Roets and Super Happy Dragon Lucky

```
Copyright 2016 Joe Roets and Super Happy Dragon Lucky

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

---

[Full Released Document PDF](/doc/DragonchainArchitecture.pdf)
