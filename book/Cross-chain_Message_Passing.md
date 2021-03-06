### 4.4.3 跨链消息传递（XCMP）  
&emsp;&emsp;XCMP是平行链用来相互发送消息的协议。它旨在保证四件事：第一，信息快速到达；第二，从一个平行链的信息按顺序到达另一个平行链；第三，在发送信息的平行链历史数据中，到达的信息确实已被发送；第四，接收者将公平地收到不同发送者的信息，帮助保证发送者不会无限期地等待其信息被看到。  
&emsp;&emsp;XCMP有两个部分。(1) 一个平行链区块的发送信息的元数据被包含在中继链上，随后这些元数据被接收平行链用来验证信息。(2) 与该元数据相对应的信息体需要从发送者实际分发到接收者，同时还要证明该信息体确实与相关元数据相关。分发的细节作为网络协议在跨链消息中有所涉及；其余部分在下文中介绍。  
&emsp;&emsp;中继链块包括平行链区块头的方式给了平行链区块一个同步的时间概念，通过中继链块的编号。此外，它允许我们在中继链给出的历史中验证消息的发送，也就是说，不可能出现一个平行链发送了一个消息，然后重新排序，使该消息没有被发送，而是被收到。即使系统可能没有对信息是否被发送达成最终结论，这也是成立的，因为任何中继链都提供了一个一致的历史。  
&emsp;&emsp;因为我们要求平行链最终对每条消息采取行动，不交付一条消息就有可能使平行链无法建立区块。因此，我们的消息传递系统需要足够的冗余度。任何验证PoV区块的验证人都应该将该区块的任何发送消息保留一天左右，并且发送区块的所有全节点也会储存外发消息，直到他们知道它们已经被采取行动。  
&emsp;&emsp;为了实现一致性，当作为发送源的平行链S向接收方平行链D发送平行链B中的消息时，那么我们就需要使用中继链状态来验证这些消息，而中继链状态是根据中继链中包含的平行链B对应的平行链区块头PH来更新的。我们需要在中继链状态中限制像PH这样的头文件的数据量，同时也要限制中继链在处理这种平行链头文件时需要做的认证工作。  
&emsp;&emsp;为此，平行链头PH包含一个发送消息的消息根M，以及一个表明该区块中哪些其他平行链被发送消息的位域。消息根M是该区块发送消息的每个副链p的头哈希Hp的Merkle树的根。头HD的哈希链有所有从D发送至S的信息的哈希值，不仅仅是在块B中，而是在块B之前的任何块中从S发送至D的信息。这允许从S到D的许多消息依次从M处被验证。无论消息本身如何被传递，它们也应该与Merkle证明一起被发送，该证明允许接收平行链的节点认证它们是由头PH在特定中继链块中的B发送的。  
&emsp;&emsp;平行链s按顺序接收传入的消息。在内部，平行链s可以根据他们自己的逻辑（可能受到SPREE的约束，见A.1）推迟或重新安排对消息的作用。然而，他们必须按照中继链给出的一致历史所决定的顺序来接收消息。一个平行链D总是先接收由其头在早期中继链块中的平行链块发送的消息。当几个这样的源平行链在中继链块中有一个区块头时，来自这些平行链的消息会以某种预定的平行链顺序被接收，可以按平行链id增加的顺序，也可以是某种洗牌的版本。  
&emsp;&emsp;一个平行链D接收一个平行链S在一个平行链块中发送的所有信息，或者不接收任何信息。D的一个平行链头PH’包含一个水印。这个水印由一个中继链块R的块号和一个源平行链S的平行链id组成。这表明D已经收到了中继链块R之前的所有链所发送的所有消息，并且在排序中对R块中由平行链（包括S）发送的消息进行了操作。  
&emsp;&emsp;水印必须在Ds的每个平行链块中至少领先一个发送平行链，这意味着水印的中继链块数超前或保持不变，我们只超前平行链。如果要在建立在某一特定中继链块R上的平行链D上产生一个平行链区块，收集人需要查看该链的最后一个平行链块所建立的中继链块之间有哪些准链头。此外，它还需要每一个表明他们向D发送消息的人的相应信息数据。因此，它可以构建一个PoV块，以便STVF可以验证所有这些消息被采取行动。由于一个平行链必须接受所有发送给它的消息，我们为平行链实现了一种方法，使另一个平行链向它发送的任何消息都是非法的，这些消息可以在发生垃圾邮件的情况下使用。当发送消息的平行链块的头被包含在中继链块中时，那么任何连接到源和目的平行链网络的节点都应该将消息连同其证明从发送者转发给接收者。中继链至少应该起到备份作用：D的接收方平行链验证人与D的平行链网络相连，如果他们没有在该网络上收到消息，那么他们可以在消息发送时向发送链S的平行链验证人索要消息。  
