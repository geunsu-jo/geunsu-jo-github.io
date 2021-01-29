---
layout: single   
title: "[Time Series Analysis]시계열분석에서 ACF와 PCFA"   
excerpt: "ACF와 PCFA에 대해서 알아봅니다."   
tags:
  - time series
  - ACF
  - PCFA
  - R
comments: true
toc: true
categories:
  - time series   
use_math: true   
output:
  md_document:
    variant: markdown_github
    preserve_yaml: true
---



ACF와 PCFA는 모형을 식별하기 위한 도구입니다. 이 둘을 이해하기에 앞서
시도표(time plot)에 대해 알아보겠습니다.

\#\#시도표(Time plot)

시도표는 x축에는 시간, y축에는 관찰값을 놓고 두 값이 상응하는
점(point)를 선으로 연결한 그림입니다. 시계열의 정상성 가정을 검정하는
예비단계로써, 차분(differencing)이 필요한지, 차분이 필요하다면 어떤
차수를 선택할지 시도표를 보고 판단합니다. <br>

> 모수절약적 관점(parsimony policy)에서 차분은 가능한 적을수록 좋습니다.
> 우선적으로 1차 차분 후에도 추세가 남았다고 판단되면 2차 차분을 취하는
> 형식으로 차분을 늘리는 것이 바람직합니다.

``` r
dd1=matrix(c(1342, 1442, 1252, 1343, 1425, 1362, 1456, 1272, 1243,
1359,1412, 1253, 1201, 1478, 1322, 1406, 1254, 1289, 1497, 1208))
dd1.ts=ts(data=dd1, start=c(2006,1), frequency=4)
dd1.ts
```

    ##      Qtr1 Qtr2 Qtr3 Qtr4
    ## 2006 1342 1442 1252 1343
    ## 2007 1425 1362 1456 1272
    ## 2008 1243 1359 1412 1253
    ## 2009 1201 1478 1322 1406
    ## 2010 1254 1289 1497 1208

``` r
plot(dd1.ts, main='Random variation Time Series')
```

![](/assets/images/time_series/dd1_plot-1.png)
