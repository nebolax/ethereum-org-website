---
title: 提议者-构建者分离
description: 学习以太坊验证者如何以及为什么要将区块构建和区块传播职责分离。
lang: zh
---

# 提议者-构建者分离 {#proposer-builder-separation}

目前，以太坊验证者既构建区块，_也_广播区块。 他们将通过传播网络接收的交易打包成一个区块，然后发送到以太坊网络的对等节点。 **提议者-构建者分离 (PBS) **将这些工作分给多个验证者。 区块构建者将负责创建区块，并在每个时隙将它们提供给区块提议者。 区块提议者无法看到区块的内容，只是选择收益最高的区块，并向区块构建者支付费用，然后将区块发送给对等节点。

进行这次重要升级有多项原因。 首先，它在协议级别创造了防止交易审查的机会。 其次，它防止可以更好地优化区块构建的盈利能力的机构参与者取代业余验证者。 第三，它通过推动 Danksharding 升级帮助拓展了以太坊。

## 提议者-构建者分离和抗审查性 {#pbs-and-censorship-resistance}

将区块构建者和区块提议者分开，大大增加了区块构建者审查交易的难度。 这是因为它可以添加相对复杂的纳入标准，确保在区块提出之前不进行任何审查。 由于区块提议者与区块构建者是不同的实体，它可以承担保护者的角色，防范区块构建者的审查。

例如，可以引入纳入清单，以确保当验证者知道交易但看到它们没有被添加到区块中时，会将其作为下一个区块中必须添加的交易。 纳入清单由区块提议者的本地内存池（其知悉的交易列表）生成，并在提出区块之前发送给对等节点。 如果纳入清单中的任何交易缺失，提议者可以拒绝该区块，在提出区块之前添加缺失的交易，或者提出该区块，然后由其他验证者在接收时拒绝它。 这一想法还有一个可能更高效的版本，要求构建者必须充分利用可用的区块空间，如果他们没有这样做，则从提议者的纳入清单中添加交易。 这一领域仍在研究阶段，纳入清单的最佳配置尚未确定。

[加密内存池](https://www.youtube.com/watch?v=fHDjgFcha0M&list=PLpktWkixc1gUqkyc1-iE6TT0RWQTBJELe&index=3)也可以使构建者和提议者在区块广播之后才能知道他们在区块中添加了哪些交易。

<ExpandableCard title="提议者-构建者分离解决了哪些类型的审查问题？" eventCategory="/roadmap/pbs" eventName="clicked what kinds of censorship does PBS solve?">

强大的组织可以对验证者施压，让其对特定地址的交易进行审查。 选择屈服这种压力的验证者会在其交易池中检测被列入黑名单的地址，并将它们从其提出的区块中排除。 在提议者-构建者分离之后，这种情况将不可能再发生，因为区块提议者将不知道他们在区块中要广播哪些交易。 对于特定个体或应用程序，可能需要遵守审查规则，例如当他们所在地区的审查规则成为法律要求时。 在这些情况下，要在应用程序层面遵循审查要求，而协议仍然应保持无需许可和免受审查的状态。

</ExpandableCard>

## 提议者-构建者分离和最大可提取价值 {#pbs-and-mev}

**最大可提取价值 (MEV)**是指验证者通过对交易进行对其有利的排序，使其收益最大化。 常见的例子包括在去中心化交易所进行套利交换（如提前进行大额买卖）或寻找机会清算去中心化金融头寸。 将最大可提取价值最大化需要复杂的技术知识，还需要普通验证者安装定制软件，因此机构运营商在获取最大可提取价值方面极有可能胜过个人和业余验证者。 这意味着，中心化运营商的质押回报可能更高，从而形成一种中心化力量，抑制自行质押。

提议者-构建者分离通过重新配置最大可提取价值的经济原理解决了这一问题。 区块提议者无需自行搜索最大可提取价值，只需从区块构建者提供的众多区块中挑选一个即可。 区块构建者可能已经完成了复杂的最大可提取价值提取工作，但其奖励归区块提议者所有。 这意味着，即使一小部分专业的区块构建者主导了最大可提取价值的提取，但奖励由网络上的任何验证者（包括个体和家庭质押人）获得。

<ExpandableCard title="为什么可以集中构建区块？" eventCategory="/roadmap/pbs" eventName="clicked why is it OK to centralize block building?">

由于复杂的最大可提取价值策略提供了更高的回报，可以激励个人使用资金池质押，而非使用自有资金质押。 将区块构建与区块提出分开，意味着提取的最大可提取价值将被分配给更多的验证者，而不是集中在可以最有效地搜索最大可提取价值的验证者。 同时，允许专门的区块构建者存在，可以减轻个人构建区块的负担，也可以防止个人为自己窃取最大可提取值，同时最大程度上增加能够验证区块的诚实性的独立个人验证者的数量。 “证明者与验证者的不对称性”是一项重要概念，是指只要有一个强大的、最大程度去中心化的验证者网络能够证明区块的诚实性，就可以接受中心化的区块生成。 去中心化只是一种手段，而不是最终目标 - 我们想要的是诚实的区块链。
</ExpandableCard>

## 提议者-构建者分离和 Danksharding {#pbs-and-danksharding}

Danksharding 是可以让以太坊扩展到每秒处理 >100,000 笔交易并最大限度降低卷叠用户手续费的方法。 它依赖于提议者-构建者分离，因为它增加了区块构建者的工作量，要求区块构建者在不到 1 秒的时间内为高达 64 MB 的卷叠数据计算证明。 这可能需要能够为这项工作专门投入非常多的硬件的专业区块构建者。 然而，在目前的情况下，由于最大可提取价值的提取，区块构建可能会越来越集中到更先进、更强大的运营商。 提议者-构建者分离是接受这一现实的一种方式，可以防止其推动区块验证（重要部分）或质押奖励分配的中心化。 此外，还有一个重要的附带好处，那就是专业的区块构建者也愿意并能够计算 Danksharding 所需的数据证明。

## 当前进展 {#current-progress}

现在，提议者-构建者分离正处于高级研究阶段，但是在以太坊客户端推出其原型之前，仍然有一些重要的设计问题需要解决。 目前还没有最终确定的规范。 这意味着实现提议者-构建者分离可能还需要一年甚至更长时间。 点击查看最新[研究现状](https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance)。

## 延伸阅读 {#further-reading}

- [研究现状：提议者-构建者分离的抗审查性](https://notes.ethereum.org/@vbuterin/pbs_censorship_resistance)
- [有利于提议者-构建者分离的收费市场设计](https://ethresear.ch/t/proposer-block-builder-separation-friendly-fee-market-designs/9725)
- [提议者-构建者分离和抗审查性](https://notes.ethereum.org/@fradamt/H1TsYRfJc#Secondary-auctions)
- [纳入清单](https://notes.ethereum.org/@fradamt/H1ZqdtrBF)