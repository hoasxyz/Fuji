---
title: R语言提高运算效率
author: hoas
date: '2021-01-29'
slug: improve-computing-efficiency-in-r
categories:
  - notes
tags:
  - R
---

一些提高R语言运算效率的方法，尤其是循环。

## data.table::set()

http://brooksandrew.github.io/simpleblog/articles/advanced-data-table/#fast-looping-with-set.

New function set(DT,i,j,value) allows fast assignment to elements
of DT. Similar to `:=` but avoids the overhead of [.data.table, so is
much faster inside a loop. Less flexible than `:=`, but as flexible
as matrix sub-assignment. Similar in spirit to `setnames()`, `setcolorder()`,
`setkey()` and `setattr()`; i.e., assigns by reference with no copy at all.

```r
M = matrix(1,nrow=100000,ncol=100)
DF = as.data.frame(M)
DT = as.data.table(M)
system.time(for (i in 1:1000) DF[i,1L] <- i)   # 591.000s
system.time(for (i in 1:1000) DT[i,V1:=i])     #   1.158s
system.time(for (i in 1:1000) M[i,1L] <- i)    #   0.016s
system.time(for (i in 1:1000) set(DT,i,1L,i))  #   0.027s

```