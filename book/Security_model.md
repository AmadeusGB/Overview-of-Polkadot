# 2.概要
&emsp;&emsp;本节目的是描述Polkadot的主要功能，而不会详细介绍设计方案和推论。  

&emsp;&emsp;Polkadot系统由一个称为中继链的开放协作去中心化网络构成，该网络与许多其他并行运行的外部链交互，称为平行链。从上层的角度来看，平行链是中继链的客户端，中继链为这些客户端提供安全服务，包括安全通信。这是中继链的唯一目的；平行链是提供应用程序级功能的载体，例如加密货币。  

&emsp;&emsp;平行链的内部细节不是中继链关心的问题；平行链只需要在我们指定的接口上显示。其中一些期望是基于区块链的基础组成部分，因而得名。但是，其他非区块链系统也可以作为Polkadot平行链运行，只要它们满足该接口即可。如下所述：相关部分加下划线。  

&emsp;&emsp;这些方面可以简称为，Polkadot是一个可扩展的异构多链协议。
## 2.1 安全模型
&emsp;&emsp;我们假设平行链作为中继链的外部不可信客户端运行，并且中继链仅通过接口处理平行链，而不对其内部进行假设。例如，在内部它们可能为许可或开放网络；如果一些内部用户破坏了平行链内部结构，从Polkadot的角度来看，该平行链（作为单个客户端载体）都是恶意的。  

&emsp;&emsp;作为开放的去中心化网络，Polkadot中继链需要内部处理某种程度的恶意行为。特定的单个节点是不可信的，但大小下限的不可确定的节点子集是可信的，该协议的作用是确保外部的中继链作为一个整体是可信的。有关详细信息，请参阅第3.2节。
