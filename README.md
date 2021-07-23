# <p align="center">Polkadot概述及其设计方案</p>  
<p align="center">Jeff Burdges<sup>1</sup>, Alfonson Cevallos<sup>1</sup>, Peter Czaban<sup>1</sup>, Rob Habermeier<sup>2</sup></p> <p align="center">Syed Hosseini<sup>1</sup>, Fabio Lama<sup>1</sup>, Handan Kılın¸ c Alper<sup>1</sup></p><p align="center">Ximin Luo<sup>1</sup>, Fatemeh Shirazi<sup>1</sup>, Alistair Stewart<sup>1</sup>, Gavin Wood<sup>1,2</sup></p>  
<p align="center"><sup>1</sup>Web3 Foundation,</p>  
<p align="center"><sup>2</sup>Parity Technologies</p>  
<p align="center">June 1, 2020</p>  
  
<p align="center">译者：郭斌(amadeusgb123@gmail.com)</p>
<p align="center">路路(xx@gmail.com)</p>  
<p align="center">Faye Wang(xx@gmail.com)</p>  


## <p align="center">摘 要</p>  
&emsp;&emsp;在本文中，我们描述了异构多链协议 Polkadot 的设计组件，并解释了这些组件如何帮助Polkadot解决区块链技术的一些现有缺点。目前，已经引入和使用的很多区块链项目，可能在设计时未考虑相互协作的能力。这导致用户难以在不同的区块链之间使用大量交互的应用程序。此外，随着项目数量的增加，每个项目单独提供的安全性变得越来越弱。Polkadot旨在为多条链提供一个可扩展且可互操作的框架，该框架具有池化安全性，这是通过本文中描述的组件集合实现的。


- [1 简介](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Introduction.md)
- [2 概要](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Security_model.md)
  - [2.1 安全模型](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Security_model.md)
  - [2.2 节点与角色](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Nodes_and_roles.md)
  - [2.3 协议](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Protocol.md)
- [3 序言](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Roles.md)
  - [3.1 角色定义](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Roles.md)
  - [3.2 Polkadot对抗模型](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Adversarial_Model_of_Polkadot.md)
- [4 组件和子协议](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Nominated_proof-of-stake_and_validator_election.md)
  - [4.1 NPoS和验证人选举](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Nominated_proof-of-stake_and_validator_election.md)
  - [4.2 中继链状态机](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Relay_Chain_State_Machine.md)
  - [4.3 共识](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Blind_Assignment_for_Blockchain_Extension.md)
    - [4.3.1 BABE](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Blind_Assignment_for_Blockchain_Extension.md)
    - [4.3.2 GRANDPA](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/GRANDPA.md)
  - [4.4 平行链](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Block_Production.md)
    - [4.4.1 区块生成](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Block_Production.md)
    - [4.4.2 有效性和可得性](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Validity_and_Availability.md)
    - [4.4.3 跨链消息传递（XCMP）](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Cross-chain_Message_Passing.md)
  - [4.5 经济激励层](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Staking_rewards_and_inflation.md)
    - [4.5.1 质押奖励和通胀设计](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Staking_rewards_and_inflation.md)
    - [4.5.2 中继区块限制和交易费设计](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Relay-chain_block_limits_and_transaction_fees.md)
  - [4.6 治理](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Proposals_and_Referenda.md)
    - [4.6.1 提案和公投](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Proposals_and_Referenda.md)
    - [4.6.2 理事会和技术委员会](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/The_Council_and_the_Technical_Committee.md)
    - [4.6.3 平行链卡槽配置](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Allocation_of_parachain_slots.md)
    - [4.6.4 国库](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Treasury.md)
  - [4.7 密码学](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Account_keys.md)
    - [4.7.1 账户密钥](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Account_keys.md)
    - [4.7.2 会话密钥](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Session_keys.md)
  - [4.8 网络](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Networking_overview.md)
    - [4.8.1 网络概况](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Networking_overview.md)
    - [4.8.2 Gossiping](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Gossiping.md)
    - [4.8.3 分发服务](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Distributed_service.md)
    - [4.8.4 存储和可用性](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Storage_and_availability.md)
    - [4.8.5 跨链消息](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Cross-chain_message.md)
    - [4.8.6 哨兵节点](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Sentry_nodes.md)
    - [4.8.7 授权、传输和发现](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Authentication_transport_and_discovery.md)
- [5 远期计划](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Future_Work.md)
- [附录A](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/SPREE.md)
  - [A.1 SPREE](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/SPREE.md)
  - [A.2 与外链的互操作性](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Interoperability_with_External_Chains.md)
  - [A.3 与其他多链系统的对比](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/ETH2.0.md)
    - [A.3.1 ETH2.0](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/ETH2.0.md)
    - [A.3.2 Sidechains](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Sidechains.md)
    - [A.3.3 Cosmos](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Cosmos.md)
- [词汇表B](https://github.com/AmadeusGB/Overview-of-Polkadot/blob/main/book/Glossary.md)

