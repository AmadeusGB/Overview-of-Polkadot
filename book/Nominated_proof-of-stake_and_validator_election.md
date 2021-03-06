# 4 组件和子协议
&emsp;&emsp;接下来，我们简洁而全面地总结波卡的功能性，以及继续阐述单独的组件和子协议。  

&emsp;&emsp;波卡的验证人采用NPoS选择机制（第4.1节）。NPoS（Nominated Proof-of-Stake）是基于对PoS的改进，它允许任意数量的代币持有者以提名人的身份参与到网络中，他们的质押支持了大量但有限的验证人集合。这个示例同时实现了高安全性和可扩展性，这个范式可同时实现高安全性和可扩展性，并通过投票机制中为人所共知的比例代表制，来达到一个前所未有的去中心化水平。提名人在经济上赋予系统安全性，对验证人的表现起着监督者的作用。基于提名人对候选人表达的偏好，每一个era系统都会选择一组获得的质押数量支持尽可能高的、提名人分布尽可能均匀的验证人集合。同时，提名人也可能会因为把自己的选票贡献给了太少数量的验证人而在经济上不受激励，这有助于随着时间的推移持续保持系统的分散度。此外，选举机制对突然的变化具有很强的适应性，比如一些验证人在被大幅削减后被踢出，选举机制此时会自动在一组新的验证人之间重新分配提名人的支持，即使选票本身没有变化。  
&emsp;&emsp;波卡的安全性目标是参与者在理性的的情况下实现拜占庭容错（见第4.5节的关于激励和经济的更多细节）。我们认为在NPoS机制下，质押人选择的一组验证人集合中，至少有2/3的验证人是诚实的。  
&emsp;&emsp;被选中的验证人集合负责运行中继链（第4.2节）。每个平行链的收集人负责生产平行链区块（第4.4.1节），验证人被分为轮动的子集，一个平行链一个子集，在这些平行链区块的区块头被纳入中继链之前需要被验证有效性。  
&emsp;&emsp;为了实现良好的可扩展性，每个子集中的验证人数量都很小。尽管如此， NPoS机制保证每个验证人节点获得良好支持的，可用性和有效性机制（第4.4.2节）可以确保任何针对波卡有效性的攻击都是可预期且非常昂贵的。实际上，波卡的整体经济安全性会支持到每一条平行链。这和最初的想法形成鲜明对比：比如使得100条独立的平行链拥有价值相等的质押数量，即平均每条平行链被1/100的总质押支持，所以每条平行链只能从1/100的安全性中获益。我们通过对每个平行链进行擦除编码来保证可用性，使验证人对这些平行链区块的可用性负有共同责任，而不会破坏其可扩展性。  
&emsp;&emsp;为了使其发挥作用，我们需要能够平行链具备回滚功能，直到我们大概率确定所有平行链都是正确的。这意味着，我们需要具备重构链的能力，为此，链需要能够进行分叉。因此我们采取了BABE（第.3.1节）作为块生成机制，虽然由验证人运行，但具有类似PoW链的特性。具体来说，我们采用最长链规则作为我们共识的一部分，所以并不会预先知道谁是下一个区块生产者。就其本身而言，BABE要求我们从一个区块产生的那一刻起，到它被最终确定的那一刻，即当我们可以确信该区块永远不会被回滚时，需要等待很长的时间。在某些情况下，为了确保区块的可用性，需要使终态化进程放缓。但大多数时候，我们更倾向于快速确认区块终态。为此，验证人使用GRANDPA（第4.3节）来确定区块的终态，这是一个与区块生成完全分离的终态敲定工具。BABE和GRANDPA的分离运行，使得GRANDPA具有自动调节性，并允许我们延迟确认区块终态直到其可用性被验证，但这并不会减缓区块的生成速度。GRANDPA在确定一个区块终态时使用拜占庭协议，并允许我们向跟踪验证人集合的实体证明哪些块已经被确定终态，这对转接桥来说很重要（见附录A.2）。  
&emsp;&emsp;如果一个平行链上的账户给另一个平行链上的账户发送代币，那么XCMP（第4.4.3节）保证这条信息被准确传递。它发送的速度不依赖于区块被终态化需要的时间，这意味着信息发送过程中需要处理波卡可能分叉的情况。因此，我们会乐观执行，基于假定平行链区块都是正确的。如果有一个区块不正确，就需要回滚它，同时，重要的是平行链只接收来自于新的中继链分叉上的区块发送信息，而不是接收被回滚了的分支上的信息。因此，我们需要平行链和XCMP逻辑来确保中继链的一个分叉定义了一个一致的Polkadot历史数据，因此消息只有在这个中继链分叉定义的历史中被发送时才会到达。  
&emsp;&emsp;如果代币的交易结合SPREE模块（附录A.1) 来进行，则可以保证只要平行链正确运行，代币只能以约定的方式被创建和销毁，同时可用性和有效性机制保证了链上代码的正确执行。SPREE也确保代币交易逻辑所需的共享代码是正确的。即使平行链可以改变他们自己的代码，却不能改变SPREE模块的代码。相反，SPREE模块的代码是集中存储的，该代码的执行及存储将与状态转换的其他部分进行沙盒处理。这就保证了代币交易消息被正确解析，且确保我们获得了想要代币的保证。  
&emsp;&emsp;在经济方面(第4.5节)，我们的目标是有一个可控制的接近恒定的年通货膨胀率。如前所述，对于系统的安全性来说，非常重要的是所有验证人都有大量的质押金支持。我们为验证人和支持他们的提名人制定了自适应的奖励计划，确保了NPoS的整体参与度保持在较高水平，并且验证人的质押支持是均匀分布的。在更细颗粒度的层面上，我们会根据验证人每次的执行行为来其进行支付或削减，并按比例对提名人进行同样的奖励或惩罚，以确保合理的策略和诚实的行为相一致。  
&emsp;&emsp;中继链自身的逻辑会不定期的升级。治理机制（第4.6节）允许波卡代币持有者参与到决策流程，而不是通过中心化的权利来对系统作出任何改变——或者是类似一些去中心化系统，通过团队开发者来决定，他们一次有争议的代码改变通常会导致区块链陷入僵局或永久分岔。我们希望有一个机制可以平衡系统，可以在需要的时候快速做出没有争议的改变，同时也可以有工具对有争议的提议做出果断且正确的回应。波卡最终的决定，对所有重要的决定比如代码改变，通过DOT代币持有者参与的质押加权的民主公投来决定如何回应。被选举出来的委员会，负责做小一点的决定，以及在一些情况下被适当的被赋予了公投的优先权，如此他们便不能阻止大多数人都想要的改变。  
&emsp;&emsp;最后，我们回顾了Polkadot子协议使用的一些原语，如第4.7节和第4.8节中分别介绍的密码学和网络方案。Polkadot需要将无需信任的单链标准的点对点通信网络扩展到多链系统，在多链系统中，任何节点的网络流量都不应随系统的总数据而膨胀。  
## 4.1 NPoS和验证人选举
&emsp;&emsp;波卡原生代币是DOT。出块采用自定义的提名人权益证明NPoS，是具有确定性终态的共识协议，例如在 Polkadot中，需要一组注册的验证人集合，其规模是有数量有限的。 Polkadot 将维护包含成百上千个（nvai）数量级的验证人集合。这个数字最终由治理决定，并随着平行链数量的增加线性增长；但验证人的数量将独立于网络中的用户数量，以确保网络的可扩展性。NPoS允许数量不限的 DOT 持有者作为提名人参与，他们通过质押来提供更多价值，以帮助网络维持高水平的安全性。因此，NPoS不仅比工作量证明（PoW）更有效，而且比传统形式的PoS（如DPoS和BPoS）更安全。即我们引入了迄今为止任何其他基于PoS的区块链都无法比拟的去中心化新的保证方式。  
&emsp;&emsp;根据提名人的参数设置，在每个era开始时会选出一组新的验证人来为这个era服务，一个era大约为一天的时间（见附录中的表1）。更准确地说，任何 DOT 持有者都可以选择成为验证人候选人或提名人。每个候选人的参数都表明了它获得的质押数量支持以及它希望获得的佣金回报。反过来，每个提名人则需要锁定一些质押并公布一个包含它信任的任意数量候选人的列表。然后，下面讨论的公共协议将这些列表作为输入，并选出支持最多的候选人作为下一个era的验证人。  
&emsp;&emsp;提名人通过质押DOT支持验证人，并获得相应他们DOT质押占比的奖励或削减；更多详细信息，请参阅第4.5节。因此提名人在经济上被激励充当系统的监督者，他们将根据验证人的质押水平、佣金、过去的表现和安全性实践等参数来确定他们将选择验证人。我们的方案允许系统选举拥有大量股权的验证人——DOT 持有量远高于任何一方的——从而有助于将验证人选举过程变成精英选举而不是富豪统治。事实上，在任何给定时刻，我们希望所有的DOT 供应中有相当大一部分都被质押在NPoS中。这使得对抗性实体很难让其验证人当选，因为它需要大量的DOT或足够高的声誉来获得所需的提名人的支持，而且攻击成本很高，因为它可能会失去所有的股权和赢得的声誉。  
&emsp;&emsp;Polkadot 通过去中心化协议选举验证人，该协议具有精心挑选的、简单的和公开的规则，将提名人的可信候选人名单作为输入。从形式上看，该协议解决了一个基于批准投票的多赢家选举问题，其中提名人的投票权与他们的利益成正比，目标是去中心化和安全。  
&emsp;&emsp;**去中心化**：我们的去中心化目标转化为投票理论中的经典概念：比例代表。也就是说，也就是说，一个委员会应该代表选民中的每一个少数群体，与他们的总票数（在这种情况下，他们的利益）成正比，没有一个少数群体的代表人数不足。我们在此强调，提名人--以及他们所信任的候选人名单--是衡量社会大众偏好的重要标准，不同的偏好和派别自然会出现，不仅是由于经济和安全方面的原因，还有政治、地理等原因。在一个分散的社区中，这种观点的多样性是值得期待和欢迎的，重要的是让所有少数民族参与决策过程，以确保用户的满意度。  
&emsp;&emsp;设计一种实现比例代表制的选举制度的想法在文献中已经存在了很长时间。特别值得注意的是斯堪的纳维亚数学数学家 Edvard Phragmen 和 Thorvald Thiele 在19世纪末的工作。最近，学术界作出了相当大的努力来正式确定比例代表制的概念，并重新审视了Phragm´en和Thiele的方法，在算法上对其进行优化。我们的验证人选择协议是对Phragm´en方法的改进，且确保遵守比例合理代表制 (PJR) [29, 7] 的技术特性。从形式上看，如果每个提名人n∈N，质押 staken 并支持一组候选人C_n⊆C，协议就会选择一组n_val验证人V⊆C ，如果这里有少数提名人N'⊆N，那么:  

![公式1](../src/f1.png)   

&emsp;&emsp;**安全性**: 如果一个提名人得到两个或更多受信任的候选人，从而当选为验证人，共识协议还必须确定如何分配提名人的质押份额并将收益分配给他们。相对应地，这些分配说明了每个验证人接收到的总质押支持。我们的目标是使这些验证人的支持率尽可能高且平衡。特别是，我们专注于最大化最小验证人的支持。直观地说，最小支持度对应于对手获得对一个验证人的控制成本的下限，也对应于因不当行为带来的可削减金额的下限。  
&emsp;&emsp;如果每个提名人n∈N质押stake_n，且支持候选人子集C_n⊆C，那么该协议不仅必须选出一组V⊆C拥有PJR属性的验证人nval，而且还需要根据提名人支持且被选中的验证人中定义一个分布，即函数f:N×V→R_(≥0)  

![公式2](../src/f2.png) 

&emsp;&emsp;这个目标所定义的问题在文献[30]中被称为最大化支持，并且已知是NP-hard。我们已经为它开发了几种有效的算法，这些算法提供了理论上的保证（常数因子的近似值），也有很好的扩展性，并在我们的测试网上成功测试。要了解更多信息，请参见我们关于NPoS[12]中验证人选举的论文。

