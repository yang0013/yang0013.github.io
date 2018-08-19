---
layout: post
title:  "arts w02-Merge Two Sorted Lists"
date:   2018-08-13 13:37:05 +0800
categories: arts
---

## Tip
[Let’s Encrypt](https://letsencrypt.org/getting-started/) 提供免费的 https 证书服务，可用工具 certbot 管理证书，安装步骤见[这里](https://certbot.eff.org/lets-encrypt/ubuntuxenial-nginx)，下面以 ubuntu 16.04 nginx 为例，如下：

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-nginx
```

安装完 certbot 后运行下面命令，根据提示，输入邮箱等信息，certbot 会获取你主机上 nginx 的配置信息，自动添加 ssl 配置项。

```
$ sudo certbot --nginx
```

过程中会询问是否将 http 重定向到 https，重定向输入 2：

```
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
```

完成之后，重启 nginx，就可以 用 https 访问站点了，上面若选 2，则访问 http 会自动跳转到 https。

## Algorithm
leetcode 21题 [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/description/)，将两个有序链表，合并成一个有序链表。

>Input: 1->2->4, 1->3->4  
Output: 1->1->2->3->4->4

第一版通过的代码：

```python
class Solution:
    def mergeTwoLists(self, l1, l2):
        if not l1:
            return l2
        if not l2:
            return l1

        if l1.val <= l2.val:
            tmp = l1.next
            l = l1
            l1 = tmp
        else:
            tmp = l2.next
            l = l2
            l2 = tmp

        res = l
        while l1 and l2:
            if l1.val <= l2.val:             
                tmp = l1.next
                l.next = l1
                l = l.next
                l1 = tmp
                
            else:
                tmp = l2.next
                l.next = l2
                l = l.next
                l2 = tmp
        
        if l1:
            l.next = l1
        if l2:
            l.next = l2
        
        return res
```

看了讨论区的这个[帖子](https://leetcode.com/problems/merge-two-sorted-lists/discuss/9735/Python-solutions-(iteratively-recursively-iteratively-in-place).)，有了一个简洁版：

```python
class Solution:
    def mergeTwoLists(self, l1, l2):
        res = l = ListNode(0)
        while l1 and l2:
            if l1.val <= l2.val:
                l.next = l1
                l1 = l1.next
            else:
                l.next = l2
                l2 = l2.next

            l = l.next
        
        l.next = l1 or l2
        
        return res.next
```
两个版本的解决思路完全一样，不同的是第二版本对新链表做了初始化，返回结果时取next；去掉循环中的 `tmp` 变量，抽出 `if` 中共同的 `l = l.next`；去掉不必要的空判断；用 `l1 or l2` 替换两个 `if` 语句。代码量少了一半，执行时间从 52ms 降到 48 ms。完美。

上周选了一个中等难度的题，解了四五个小时，本次就选了简单题，花了两个半小时。还是先从简单的开始。

## Review
[A Deeper Look at Different Smart Contract Platforms](https://blockgeeks.com/guides/different-smart-contract-platforms/)

这篇文章对比了智能合约的几个运行平台，从比特币和以太坊引出智能合约，介绍了什么是智能合约，以及智能合约的三个特性：确定性、可终止、相互隔离，并详细介绍了这三个特性是什么。接着提出一个问题，Dapp运行的条件：支持百万级用户、免费、可升级、低延迟。然后从几个点切入去对比目前可运行智能合约的几个项目：以太坊、EOS、Stellar、Cardano、Neo、Hyperledger Fabric。从智能合约的开发语言、使用费用、运行速度等角度讨论各个平台，有图有代码，最后给出了结论 no “one-size-fits-all”。

文章结构严谨，有理有据，很详细，但作者估计只是研究并不是实际的使用者，所以还是缺少一些说服力。文章中有一些我不大认同，比如 Dapp 要支持百万级用户，我们需要在区块链上再造一个 twitter、facebook 吗？即使需要，是否所有东西都要跑在区块链上？在链上存储运算的成本是非常高的。即使是文章中提到的 eos，虽然交易免费，但存储是收费的，而且初期投入的开发成本（用于抵押cpu和带宽的eos）其实并不低。另外，把 Hyperledger Fabric 和其他几个项目相提并论并不合适，其他几个都是公链，而Fabric 是联盟链，若要使用要自己搭建底层环境，而搭建 Fabric 环境非常麻烦，不然就得去购买第三方平台的区块链服务，而这和中心化也没啥区别，数据都在人家服务器上，运行的 Fabric 代码也是人家部署的，你怎么知道他没有动手脚？第三方区块链平台的信任问题现在并没有解决。所以，文章缺少了对数据安全性的考虑。

## Share
[The State of Hashing Algorithms — The Why, The How, and The Future](https://medium.com/zkcapital/the-state-of-hashing-algorithms-the-why-the-how-and-the-future-b21d5c0440de)

这篇文章介绍了哈希算法及其安全性。什么是哈希算法？给出同样的输入，输出相同的结果；任意长度的输入，产生固定长度的输出。一个好的哈希算法要满足三个条件：

* 改变输入的一个字节，输出会完全不同。
* 低碰撞率
* 效率

第一个标准的哈希算法是 md5，对于任意的输入，都会产生 128 位的输出。然而 md5 已经被认为是不安全的，无法防止碰撞，容易遭受生日攻击。什么是生日攻击？如果你任意挑选23个人，那么这23个人有两个人生日相同的概率是 50%。把人数加到 70 人，那么这个概率会上升到 99.9%。把 100 个鸽子放到 99 个格子中，必然有两个鸽子被放到相同的格子中。

所以后来演化出了 SHA1，SHA2，输出长度是 160位，然而这两个算法也被攻破。之后有了 SHA3，也就是 KECCAK。比特币使用的是 SHA256，以太坊的哈希算法是KECCAK256。

文章最后提到，哈希算法能够抵御量子计算机，是安全的。量子计算机要破解哈希算法，和今天普通的计算机使用方法是相同的：暴力破解。但量子计算机将大大减少破解的速度。



