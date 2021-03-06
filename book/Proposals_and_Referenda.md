## 4.6 治理  
&emsp;&emsp;Polkadot使用复杂的治理机制，使其能够随着时间的推移在其集合的利益相关者的最终要求下优雅地发展。一个关键和不变的规则是，对协议的所有修改必须由利益相关者加权公投同意——确保多数股权总能控制网络发挥作用。  
&emsp;&emsp;为了对网络进行任何改变，我们的想法是将DOT持有人聚集在一起，在理事会的帮助下管理一个网络升级的决定（见第4.6.2节）。无论提案是由DOT持有者还是理事会提交的，最终都必须通过全民投票，让所有DOT持有者按利益加权，做出决定。  
&emsp;&emsp;波卡的每个DOT持有者都有权：a）提交提案；b）认可一项公共提案，以便在公投时间表中优先考虑；c）对所有正在进行的公投进行投票；d）成为理事会席位的候选人；以及e）对理事会候选人进行投票。此外，任何DOT持有人都可以成为参与NPoS的提名人或验证人候选人（见第4.1节）。  
### 4.6.1 提案和公投  
&emsp;&emsp;Polkadot逻辑的核心是在链上存储在一个无定形的状态转换函数中，并以一种平台中立的语言定义：WebAssembly。每个提案都以runtime的特权函数调用的形式出现，能够修改runtime的代码本身，实现无缝升级，避免出现"硬分叉"的情况。然后，一个提案被提交，并通过公投进行表决。  
&emsp;&emsp;提案可以通过以下几种方式之一启动：  
&emsp;&emsp;- 公共提案：任何DOT持有人均可提交；  
&emsp;&emsp;- 理事会提案：由理事会成员提交；  
&emsp;&emsp;- 自动提交的提案：由前轮公投的部分内容，作为提案自动递交；  
&emsp;&emsp;- 紧急提案：由技术委员会提交（第4.6.2节）。  
&emsp;&emsp;每项经公投批准的提案都有一个相关的颁布延迟，即公投结束和修改颁布之间的时间间隔。对于前两种类型的提案，这是一个固定的时间间隔，暂定为28天；对于第三种类型，它可以根据需要设置；紧急提案处理的是网络中的重大问题，需要快速处理，因此会有一个较短的颁布延迟。颁布延迟确保了一定程度的稳定性，因为它给了所有各方足够的通知来适应新的变化。在这段时间之后，会自动调用相关的特权函数。  
&emsp;&emsp;任何利益相关者都可以通过存入固定的最低数量的DOTs来提交公开提案，并在一定时期内保持锁定。如果有人同意该提案，他们可以存入相同数量的Token来支持它。公共提案被储存在一个优先级队列中，每隔一段时间，得到最多赞同的提案会被提交给公投。一旦提案被提交，锁定的Token将被释放。  
&emsp;&emsp;理事会的提案由理事会提交，并存储在一个单独的优先级队列中，优先级由理事会自行确定。  
&emsp;&emsp;公投是一种简单的、包容的、加权的投票方案。它有一个固定的投票期，投票结束后进行统计。公投总是二元的：投票选项是 "赞成"、"反对 "或完全弃权。  
&emsp;&emsp;**时间表**：每隔30天，就会有一个新的提案被提出来，并进行公投。要提交的提案是公众提案队列或理事会提案队列中最重要的提案，如果两个队列都不空，则在这两个队列中交替进行。如果两个队列都是空的，则在公投时间表中跳过该时段。多个公投不能同时进行，但紧急公投除外，因为紧急公投的时间表是平行的。  
&emsp;&emsp;**计票**：公投的投票对所有DOT持有者开放，投票权与他们的股份成正比，最多有一个可能的投票倍数，根据一些组织对系统的承诺程度，给与他们投票权。一般来说，一个组织必须锁定他们用于投票的Token，至少要到公投结束后的颁布延迟期。这是为了确保需要对结果有一些最低限度的经济买入，并劝阻卖票。完全不锁定投票是可能的，但在这种情况下，投票权只是给定股权的正常投票的一小部分。相反，Polkadot将提供自愿延长锁定，允许任何一方通过延长他们愿意锁定其Token的时间来增加他们的投票权。这确保了长期致力于该系统的选民，愿意增加他们对公投决定的接触，在这个问题上有更大的发言权。  
&emsp;&emsp;**投票率的偏差**：以公共提案形式来要求所有利益相关者参与解决某些细小问题上似乎是不必要的。例如，要求出块时间降低5%这类问题。然而，如果没有这个规则，网络很可能是不稳定的，因为把它的控制权放在利益相关者的手中之外，会产生一种错位，可能会导致不作为或更糟。然而，通过利用投票率很少是100%这一事实，我们可以根据情况产生不同的结果，在主动和被动的利益相关者之间建立起权力平衡。例如，简单的投票系统通常会引入一个法定人数的概念，即必须达到最低的投票人数才能通过一项变革。  
&emsp;&emsp;对于公共提案，我们将这一概念概括为"积极的投票率偏差"，即假设赞成与反对的比例相同，额外的投票率总是使变革更有可能。更具体地说，在投票率低的情况下，我们通过要求超级多数的批准来支持反对方或现状，而当投票率接近100%时，要求就会降低到多数通过。这基于两个原则：首先，现状往往比任何变化都更安全，因此应该对现状有一些偏差；其次，像所有的经验测量手段一样，不可避免地会有一定程度的不准确性和波动性，特别是在投票率较低的情况下--结果可能在一个月内是51%-49%，然后变为49%-51%，考虑到颁布提案变化所涉及的成本，确保结果不可能在颁布后不久翻转是有利的。  
&emsp;&emsp;另一方面，对于理事会提交的提案，全民投票没有投票率的偏差，并遵守多数通过的原则。这里的理由是：理事会预先批准的提案被认为更安全，更不可能被推翻，所以之前提到的问题得到了缓解，我们可以让DOT持有人自由决定此事。  

![图5：自适应投票率偏差](./src/p5.png)  
<p align="center">图5：自适应投票率偏差</p>  

&emsp;&emsp;最后，在理事会提案得到所有理事会成员一致支持的特殊情况下，将观察到 "负投票率偏差"。这是第一种情况的对称反面，即额外的投票率总是使变化的可能性降低，我们在投票率低的情况下有利于赞成方，要求反对者的超级多数来拒绝提案，当投票率接近100%时，要求调低为多数通过。见图5。  
