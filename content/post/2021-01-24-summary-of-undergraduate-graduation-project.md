---
title: 本科毕设总结
author: hoas
date: '2021-01-24'
slug: summary-of-undergraduate-graduation-project
categories:
  - notes
tags:
  - Hydrology
subtitle: ''
lastmod: '2021-01-24T16:02:01+08:00'
authorLink: ''
description: ''
hiddenFromHomePage: no
hiddenFromSearch: no
featuredImage: 'https://www.oneclickevents.com.au/wp-content/uploads/2019/11/Finally-Finished.png'
featuredImagePreview: ''
toc:
  enable: yes
math:
  enable: no
lightgallery: no
license: CC BY-NC 4.0
---

又折腾了一次网站，以后轻装上阵，少弄点花里胡哨的东西，人生要慢慢做减法，网站亦是如此。

终于搞定毕设的文章了，好难，感觉自己延毕了半年……不过这又怎么样呢？有本事真正让我博士延毕半年？！

<!--more-->

## 工作流程

首先进行数据预处理，比如由于调度时段为旬，但实际数据的单位为时，因此需要进行单位转换。

然后把所有可能用到的数据放在同一个脚本文件中整理；编写可能可以降低程序运行时间的`rcpp`文件等。

附上线性插值的`rcpp`文件：

```r
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
double interpl(NumericVector x, NumericVector y, double x1) {
  int n = x.size();
  for (int i = 0; i < n; ++i){
    if (x1 >= x[i] && x1 < x[i + 1]) {
      return (x[i + 1] - x1) / (x[i + 1] - x[i]) * y[i] + (x1 - x[i]) / (x[i + 1] - x[i]) * y[i + 1];
    }
  }
}


/*** R
approx(z_v_sby[, 1], z_v_sby[, 2], 370)$y
interpl(z_v_sby[, 1], z_v_sby[, 2], 370)

approx(z_v_sby[, 2], z_v_sby[, 1], 2000)$y
interpl(z_v_sby[, 2], z_v_sby[, 1], 2000)

microbenchmark::microbenchmark(approx(z_v_sby[, 1], z_v_sby[, 2], 370)$y,
                               interpl(z_v_sby[, 1], z_v_sby[, 2], 370),
                               times = 1000)
*/
```

最后再根据整理好的数据进行计算，计算结果的保存可以使用`fs::path()`指定具体文件夹：

```r
write.csv(qem_rbf$par[qem_rbf$pareto.optimal, ], fs::path("PSO_rbf", 'g400p40_qem.csv'))
```

## 主任建议

12.31日晚年终聚餐的时候，喝多了的熊主任扶着我的肩膀说：

  1. 多和郭老师交流；
  2. 多写英文论文，一周、至少两周邮件与郭老师汇报一次；
  3. 要有一个平和、积极的心态。
  
我如此做的话，会不会和主任的距离越来越近呢？我想，应该会的，那就这样去做吧！

## 波波建议

还是很难和波波一块用餐并谈心的。原来大家一开始都是如此，需要找一个合适的角度切入与郭老师之间的话题。

> 一件事情最多和他说三遍，如果他还是不认，那么就按照他说的来。

## 自己的避坑总结

这一路走过来，从刚开始没有什么技术含量铁定被拒的《武汉大学学报（工学版）》开始，到现在的有一丢丢技术的《水资源研究》，自己给自己挖下了不少的坑，甚至是如同tctcab[所说](https://d.cosx.org/d/422020-rcpp-r/2)：

> 你这不是挖坑是在埋雷啊…

有时候确实就是自己的问题。做一项研究的时候，如果身为研究者仅仅只核查了研究结果的部分就断定整个研究的结果，那么这就是一件不负责任的行为。当我去质疑，去忽略郭老师的某些认同的时候，有没有想过错在我自己呢？

> 你再去悟吧！

> 你不要解释，我不要听你解释！

> 小谢，Tennant法是不是"nant"啊？

当然，郭老师的看法不一定完全对，**我对郭老师的看法的看法也不一定完全对**，正确的做法应该是：

对郭老师的建议，更正我的不对，并换一种对的表现方式。