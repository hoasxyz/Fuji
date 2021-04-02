---
title: R语言提高运算效率
author: hoas
date: '2021-01-29'
slug: improve-computing-efficiency-in-r
categories:
  - notes
tags:
  - R
description: "improve computing efficiency in r"
---

一些提高R语言运算效率的方法，尤其是循环或递归（Recursion）。

<!--more-->

## data.table::set()

http://brooksandrew.github.io/simpleblog/articles/advanced-data-table/#fast-looping-with-set.

New function `set(DT,i,j,value)` allows fast assignment to elements
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

## Rcpp

### interpl()

```cpp
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

### n_max()

```cpp
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
double n_max_1(double h) {
  Environment env = Environment::global_env(); 
  NumericVector turbine_h = env["turbine_h_sby"];
  NumericVector turbine_n_max = env["turbine_n_max_sby"];
  double kf = 0.9;
  int set = 4;
  int n = turbine_n_max.size();

  if (h < min(turbine_h)) h = min(turbine_h);
  for (int i = 0; i < n; ++i){
    if (h >= turbine_h[i] && h < turbine_h[i + 1]) {
      return turbine_n_max[i] * kf * set;
    } else if (h >= turbine_h[n - 1]) {
      return turbine_n_max[n - 1] * kf * set;
    }
  }
}


// [[Rcpp::export]]
double n_max_2(double h) {
  Environment env = Environment::global_env(); 
  NumericVector turbine_h = env["turbine_h_ghy"];
  NumericVector turbine_n_max = env["turbine_n_max_ghy"];
  double kf = 0.9;
  int set = 4;
  int n = turbine_n_max.size();
  
  if (h < min(turbine_h)) h = min(turbine_h);
  for (int i = 0; i < n; ++i){
    if (h >= turbine_h[i] && h < turbine_h[i + 1]) {
      return turbine_n_max[i] * kf * set;
    } else if (h >= turbine_h[n - 1]) {
      return turbine_n_max[n - 1] * kf * set;
    }
  }
}

// [[Rcpp::export]]
double n_max_3(double h) {
  Environment env = Environment::global_env(); 
  NumericVector turbine_h = env["turbine_h_gbz"];
  NumericVector turbine_n_max = env["turbine_n_max_gbz"];
  double kf = 0.933;
  int set = 3;
  int n = turbine_n_max.size();

  for (int i = 0; i < n; ++i){
    if (h >= turbine_h[i] && h < turbine_h[i + 1]) {
      return turbine_n_max[i] * kf * set;
    } else if (h >= turbine_h[n - 1]) {
      return turbine_n_max[n - 1] * kf * set;
    }
  }
}

/*** R
n_max('sby', 170)
n_max_1(170)

n_max('ghy', 99)
n_max_2(99)
n_max_2(79) # min{h} is 78m

n_max('gbz', 20)
n_max_3(20)

microbenchmark::microbenchmark(n_max('sby', 180),
                               n_max_1(180),
                               times = 1000)

microbenchmark::microbenchmark(n_max('ghy', 180),
                               n_max_2(180),
                               times = 1000)

microbenchmark::microbenchmark(n_max('gbz', 20),
                               n_max_3(20),
                               times = 1000)
*/



// else if (reservoir_no == 2) {
//   NumericVector turbine_h = env["turbine_h_ghy"];
//   NumericVector turbine_n_max = env["turbine_n_max_ghy"];
//   double kf = 0.9;
//   int set = 4;
// }
// else {
//   NumericVector turbine_h = env["turbine_h_gbz"];
//   NumericVector turbine_n_max = env["turbine_n_max_gbz"];
//   double kf = 0.933;
//   int set = 3;
// }
```

## Parallel Dynamic Programming

据绍坤所言，同一状态下相互独立的不同决策可以分配不同的核，进行并行计算，真正的递归无法使用。

参考：

- https://github.com/hsk0059/IS-PDP-and-PDP/

- https://www.researchgate.net/profile/Shaokun-He/publication/334303414_Optimal_impoundment_operation_for_cascade_reservoirs_coupling_parallel_dynamic_programming_with_importance_sampling_and_successive_approximation/links/5d9a5d7f458515c1d39c406a/Optimal-impoundment-operation-for-cascade-reservoirs-coupling-parallel-dynamic-programming-with-importance-sampling-and-successive-approximation.pdf