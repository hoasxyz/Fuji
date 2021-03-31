---
title: 考虑非一致性的运行期设计洪水的夭折
author: hoas
date: '2021-03-31'
slug: the-premature-failure-of-the-design-flood-in-operation-period-considering-nonstationary
categories:
  - notes
tags:
  - research
subtitle: ''
lastmod: '2021-03-31T14:44:40+08:00'
authorLink: ''
description: ''
toc:
  enable: yes
math:
  enable: no
license: CC BY-NC 4.0
---

<!--more-->

好了，今天是三月的最后一天，当我缓缓打出“夭折”两个字的时候，我觉得我是时候对此——考虑非一致性的运行期设计洪水，这个我进入研究生以来的第一个想法——告一个别。

有些时候，不得不说，有些思考明明已经不能再在大脑中进展，准备找个对象诉说一通的时候，进展就又有了，虽然不一定是成功的，但就算是走进了死胡同，也比徘徊更好。这可能就是要勤动笔的原因吧！

## 邮件

2 月 17 日，当我过年还在家里的时候，郭老师一个邮件发过来，邮件名叫做“熊丰博士论文V1”，邮件内容如下：

> 附件论文供学习，开学后你将继续熊丰的研究工作，
>
> 首先要学会掌握他的方法、程序和研究成果。

这就是近一个月以来我所有学习的起点。

## 学习

熊主任的博士论文《长江上游梯级水库群运行期设计洪水及汛控水位研究》，一共有六章，其中二三四章的标题为：

- 洪水地区组成方法比较研究

  通过理论推导和统计试验比较了同频率地区组成法和基于 Copula 的最可能地区组成法的异同，此章对应的是一篇 JoH 的论文。该论文多了一个因为梯级水库太多而没有考虑同频率组成法的实际应用（即只考虑了最可能地区组成）。这一章恰恰说明，包含了理论推导和基于推导的试验即使是在实例应用部分不够全的情况下也能发表，即数理统计基础上的理论推导也可以是一条可供选择的道路。

- 梯级水库群运行期设计洪水及汛控水位

  ![运行期设计洪水概念图](https://i.loli.net/2021/03/31/GdcZN2UpvnDjV4o.png)

  这是整个大论文最为精彩的一章。其基本思想是在不降低建设期设计洪水标准的情况下考虑梯级水库群不同地区的洪量组成，进而求得运行期设计洪水，再通过调洪演算计算运行期汛限水位（汛控水位，FCWL）。该想法来源于郭老师。

- 金沙江下游梯级和三峡水库防洪库容互补关系

  此章灵感来源于清江水布垭、隔河岩设计时为三峡各预留的 5 亿 m³ 防洪库容，用来推求上游水库多预留不同体积防洪库容的前提下下游水库需预留的防洪库容，试算标准为下游水库最高调洪水位不变。该想法应该还是来源于郭老师。

- 三峡水库运行期设计洪水及超标准设计洪水

  这一章考虑了上游梯级水库群调洪之后的三峡水库运行期设计洪水，其所谓的“超标准洪水”定义为“受气候变化和水利工程综合影响下的超标准设计洪水”。当然，考虑气候变化的影响一定是在未来时期。但是后面会提到，**考虑非一致性的未来时期难以和一致性情况建立等价的联系**，当然熊主任可能已经意识到了这一点，因此他将考虑气候变化的影响的结果——即在建设期的基础上根据未来降雨强度的变化同倍比放大各分区的洪水过程线，进而计算“超标准设计洪水”。

## 寻找

我大概是用了一周的时间看完了主任的大论文，然后再用了一个半周的时间去搜集前一个阶段遗留的问题的答案，前前后后大概用了两旬的时间。这是一篇不错的大论文，不错（完整）到连想找一个不那么鸡肋的加固点都没有，于是我开始搜索国内的设计洪水的研究。

知网上铺天盖地的关于“非一致性设计洪水”的研究，其中又以熊立华院长实验室居多，但是关于“运行期设计洪水”却只有郭老师和主任的文章。当时的我非常高兴：

> 如果把所有建设期设计洪水的工作在运行期设计洪水的基础上再做一遍，那岂不是？嘿嘿嘿……

现在的我回过头看看一个月前的我，**竟是如此的容易高兴，和难过一样的容易**。

当然，我的高兴也不是空穴来风。在我查看了一些关于非一致性的研究（当然也有尹家波教授关于峰量联合重现期的系列研究，但是觉得好难……）后，突然发现他们所用的模型（当时还不知道这是一个**模型**[^1]）是 GAMLSS，就在 R 中！

别提我有对高兴了！在对“梯级水库运行期设计洪水”的框架进行分析之后，我找到了两点可以考虑非一致性的地方：

![考虑非一致性的运行期设计洪水](https://i.loli.net/2021/03/31/qsnjzKvcZERbuNJ.png)

[^1]:模型：对自然系统的认识行为进行学习之后使用数理统计方法对该系统进行模拟，再根据观测资料对模拟的模型进行训练、检验和使用。

1. 基于 gamlss 的洪水频率分析提供一个非一致性情况下的设计洪量，用于地区组成的分配；
2. 基于 gamlss 的 Copula 函数提供考虑非一致性的地区组成方法。

## 模型

我并不是一开始就学习 gamlss 的，因为关于该包网上资源较少，但是我将其放在这里更符合逻辑路线（之前的思路的确不符合，才导致入门 gamlss 之后发现行不通）。

GAMLSS 的本质是一个模型，通过对拟合分布参数的 predictors （可以为有参也可以为无参[^2]）进而使分布参数不为定值，从而达到更好的模拟样本点的效果。在这里，有上百个包括连续、离散和混合在内的分布任人挑选，还可以自定义分布、链接函数，**对模型参数进行优选**等等。不能说这不是一个强悍的工具。

[^2]:分别为 parametric model 和 smoothing model，这里的（非）参数均指包含 predictors 的 terms（项）。

## 重现期

非一致性情况下，重现期并没有一致性情况下那么简单和统一。针对非一致性（当然，也包含了对一致性情况的考虑）对重现期下了新的定义：

1. 期望超过次数：在 T 年里，出现超过初始设计值的平均次数为 1；
2. 期望等待时间：从起始年起，直到下一次出现超过初始设计值的平均时间间隔为 T 年。
3. 等可靠度：一致和非一致情况下水文设计可靠度应该相同；
4. 年平均可靠度：考虑工程的使用年限；
5. 水文风险；
6. ……

这些重现期基本上都是在未来情况下（水库年限）的定义，尽管都可以考虑到一致性情况，即可以在同一个定义下进行（非）一致性的对比。一致性情况下，其设计洪水和重现期的关系图就是**水文频率曲线**。

## 勒马

我终于还是走到了悬崖边上。

在等待乐昌峡的资料之余、入门 gamlss 之后的我才意识到，考虑非一致性的运行期设计洪水走不通：若要比较一致和非一致性情况下的运行期设计洪水，就必须要建立相同定义下的重现期，而非一致性重现期对还没到设计年限的未来的考虑难以避免，就算获取了 gamlss 模型拟合所需的未来的协变量数据（predictor, or covariate），也**无法获取重现期 100 年以上非一致性水文频率的结果**，而建设期对重现期的考虑多为千年一遇以上。由于水库年限的问题，即使按照从有观测资料的那一年开始考虑设计洪水（和调度一样），资料最长也就 70 年左右，对已有资料进行设计洪水的模拟，重现期连 100 年都达不到。

至此，还没有等到研究时变 Copula，就已经宣布了结束。

悬崖那边，仿佛有半个月前的我，那个刚入门研究生的我……

## 归因

为期一个半月的学习研究依然没能进行下去，**不过也仅仅只是走进了一个不太深的死胡同里罢了**。究其原因，还是方法逻辑（logic）层面的问题，并不是其他原因导致的，对此次进入死胡同进行归因分析和总结也是有必要的。

### 研究路线

一开始我是如何踏入这条路上的呢？现在看来原因很简单很简单，那就是

- 铺天盖地的“非一致性设计洪水”和
- R 包 gamlss

前者是前人做过很多研究的东西，虽然也不算新颖（本科的时候佘老师和张利平老师似乎都有讲过），但是值得研究。而后者却体现了我的“盲动主义”：因为自己的“擅长”而盲目去学习一个新的东西，虽然也并不坏，但却与当初的研究设想分道扬镳，**并没有搞清楚研究路线的主次关系**。

蹭热点也重要，但同时也要脚踏实地，**有目的地进行寻找，在确定大致研究路线是可行的、没有严重错误的情况下再深入学习**。

### 研究节点

从最简单的开始，诸如不同方法的对比，那么首先就要寻找不同方法可用于对比的“**节点**”，即两者之间的联系。比如在本次研究中，我仅仅是考虑了不同类型的重现期，却早早地忽略了原框架下的设计标准过大，非一致性情况下完全达不到的情况，使节点的连接出现了问题。而此类研究最关键的还是得从研究节点下手，先研究两个方法能否进行比较，再分别研究方法，因为方法是现成的，研究节点得自己去找。

## 总结

### 收获

当然，这一个多月来的学习并不是没有收获的：

1. 郭老师认为非一致性和洪水地区组成是两个计算水库运行期设计洪水的方法，这是错的。运行期设计洪水完全可以考虑非一致性，不过由于实测资料太短而难以研究。
2. 非一致性的研究是还原之后的流量资料。
3. 广义极值分布是广义逆极值分布的一种（分布参数进行了简单的正负变换而已），P-III分布是 广义Gamma 分布的一种。
4. 郭老师看重的还是项目，要投其所好。
5. 主任说“考虑多变量（峰、量）联合重现期的运行期设计洪水”意义不太大。也确实是这样，相比方法的创新来讲，给之前的成果加几个小数点的工作确实意义不大。
6. Gamlss 可以用来做常模，到时候来了新数据就可以直接进行匹配了。

### 问题

存在一些问题如下：

1. 杜涛的基于 ENE 等方法的非一致性设计洪水流量图不知道是如何计算的。Gamlss 模型的分布参数随着未来的降水等因素变化，每年分布都不一样，因此各年都要分开讨论。每年一个分布、一个流量值和其它协变量，那么一年流量值对应的概率超过特定概率（**非一致性设计流量值和当年协变量对应的概率**）的情况也不一样。由于每年的协变量不同，虽然设计流量值相同，但是该设计流量值在该时段协变量情况下所对应的概率不同，因此我认为设计流量值最有可能是通过分位区间来决定的，但是这种方法似乎无法做到那么光滑；还有这个期望，也不知道如何求。
2. 全球气候模式（GCM）到底是怎么玩的？
3. Gamlss 可否用 Copula 做分布？好像可以，应该在 Additive terms中，有相关文献。
4. 期望等待时间，期望超过次数，等可靠度，年均可靠度等概念实际计算中应该如何使用？
