---
layout: post
title:  "bancor 协议简介"
date:   2018-08-12 10:06:24 +0800
categories: blockchain
---

### Bancor 是什么

Bancor 是以太坊的一个项目，是一个货币系统，通过智能合约为数字货币提供持续流动性。Bancor 解决了交易量小的数字货币的流动性。它不需要第三方的机构，也不需要第二方，通过智能合约就能买卖 Token。

>Bancor 协议使智能合约区块链上的自动价格发现和自主流动机制成为可能。这些智能代币拥有一个或者多个连接器，连接到持有其他代币的网络，允许用户直接通过智能代币的合约，按照一个持续计算以保持买入卖出交易量平衡的价格，立即为已连接的代币购买或清算智能代币。

Bancor 提出了一种价格发现机制，连接器代币(Connector Token)的权重（简称 CW：connector weight），这个参数范围在 0 至 100% 之间，设定后就被智能合约锁定，再无法修改。这个参数会作为价格发现机制的计算参数之一。BANCOR 用 ETH 作为背书并作为储备代币，CW 设置并维持在 20%。

Bancor 适合于尾部 Token 的交易，即交易量小的 Token，由于交易量小，你要买的时候可能没人卖，要卖的时候没人买。这就是小众 Token 的流动性问题。如果通过 Bancor 来发行，就不用担心这个问题。

发行的时候指定一个参数 CW，公式如下：

>CW = 连接器代币余额 / 智能代币总价值

其中
> 智能代币总价值 = 代币单价 × 智能代币总供应量

通过上面两个公式就可以推算出价格公式
 
> 代币单价 = 连接器代币余额 / (智能代币结余供应量 × CW)

当参数 CW 值不同时，价格曲线变化如下：

![different cw](http://appjiayou11.qiniudn.com/bancor-cw)


- 当 CW = 100%，可以认为发行代币是连接器代币的别名，不管需求如何变化，所发行代币的价格总是等于连接器代币的价格。
- 当 CW = 50%：所发行代币和其供应量呈线性关系。
- 当 CW < 50%，图中 CW = 10%，随着供给的增加，价格增长迅猛。
- 当 CW > 50%，图中 CW = 90%，随供给的增加，价格变化小。

由于每增加一个代币时，价格便发生变化，假如我一次性买入100个代币，那么价格应当是怎样的？Bancor 提供了一个公式：

![](http://appjiayou11.qiniudn.com/token)

卖出代币的计算公式：
![](http://appjiayou11.qiniudn.com/token withdraw)

知道兑换的代币数量和连接器代币数量，就可以计算有效价格：
>有效价格 = 参与兑换的连接器代币的数量 / 参与兑换的智能代币的数量

举个例子：假设现在有一个代币，当前总供应量为 1000，它的连接器拥有 250 数量的连接器代币，CW 值为 50%，此智能代币可按以下价格转换为其连接器代币:

>价格 = 250 / (1000 × 50%) = 0.5 

加入我要将 10 个连接器代币转换为智能代币。我会收到多少智能代币?

> 代币新发行量 = 1000 * ((1+10/250)^50%-1) ≈ 19.8

所以，我的购买价格为：
>有效价格 = 10 / 19.8 ≈ 0.5051 

使用 Bancor 好处是，价格透明、可预测，且没有交易费，只需支付以太网交易的 gas 费用。Bancor 并不适合交易量大的数字货币，比如 Bitcoin 和 ETH，这些价格变动由外部市场决定，人为的指定一个 CW 和储备金，很难跟得上外部市场的变化。而对于小交易量的数字货币，受限于其使用场景，什么时候你需要换取这些没什么交易量的货币？

### Bancor 应用场景

#### EOS RAM

EOS 的内存买卖，前段时间被炒得火热，吸引了大批投机者，EOS 内存的定价机制使用的正是 Bancor 协议，做了一点修改，买入 RAM 时 余额用的是当前储备金 EOS 余额加上本次买入的 EOS 数量，而卖出 RAM 时 balance 用的是当前 RAM 流通量减去本次卖掉的 RAM 数量。详细见这篇[RAM的Bancor协议详解](https://steemit.com/bancor/@chaimyu/ram-bancor)

#### P3D

FOMO3D 之前火了一阵的以太坊游戏，在这个游戏中，拥有 P3D 可以获取一些分红。P3D 是 FOMO3D 团队之前做的一个游戏所发行的 Token，其发行机制类似于 Bancor，也是发行的 P3D 越多，则 P3D的价格越高，只是它并不使用 Bancor 公式。价格变化规则是：每卖出一个 P3D，价格上涨 10 gwei，每买入一个 P3D，价格下降 10 gwei。（1 gwei = 0.00000001 ETH），如下：

>
P3D token1 = 1.00000000/ETH  
P3D token2 = 1.00000010/ETH  
P3D token3 = 1.00000020/ETH  
P3D token4 = 1.00000030/ETH  
P3D token5 = 1.00000040/ETH 

关于价格的计算，P3D 官网提供了详细的公式推导，见[这里](https://powh3d.eu/math)，比 Bancor 的简单些。由于 Bancor 的价格计算要使用到方根和次方，而以太坊的 EVM 不支持这些数学函数，所以 Bancor 合约中在计算价格时做了一些转换，计算近似值，很难懂。这点还是 EOS 方便，支持 pow、log。当然，这是题外话了。

除了这几个场景，bancor 还可以怎么用？积分系统是一个场景，用户每次购买商品，都返一定额度的积分，这可在结账过程中自动完成，积分可在用户间自由交换。商家也可提供指定商品，只能通过积分来购买，从而回收积分，以免积分的价格过高，用户返利少。还有，公司发行的股份也可以通过 Bancor 来做，购买来的股份也是一种代币，可以直接购买公司所提供的服务，中间的代币及价格转换都在系统中自动完成（所有通过 Bancor 发行的代币组成一个网络，任何两个代币之间可以自由转换）。另外，可以设定持有股份者获取分红，这类似与 FOMO3D 的 P3D。

参考  
[黄金、法币、班科](http://bigshark.club/2017/07/27/gold-money-bancor/)  
[RAM的Bancor协议详解](https://steemit.com/bancor/@chaimyu/ram-bancor)  
[Bancor协议：—种分层货币系统兼去中心化交易所](http://8btc.com/thread-49136-1-1.html)   
[Bancor 中文白皮书](https://github.com/FIBOSIO/bancor/raw/master/Bancor_cn.pdf)  
[Bancor 英文白皮书](https://storage.googleapis.com/website-bancor/2018/04/01ba8253-bancor_protocol_whitepaper_en.pdf)  
[bancor contract](https://github.com/bancorprotocol/contracts)  
[Bancor wiki](https://en.wikipedia.org/wiki/Bancor)