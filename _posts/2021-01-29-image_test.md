layout: single   
title:  "이미지 출력되는지 확인해보자"   
excerpt: "이미지 출력이 가능한지 테스트 중 입니다."   
tags:    
  - 123   
  - ㅇㅇ   
comments: true   
toc: true   
categories:    
  - test   
use_math: true   
output:
  md_document:
    variant: markdown_github
    preserve_yaml: true
---

#### 가장 먼저 필요한 패키지를 불러온다

``` r
library(tidyverse)
```

-   tidyverse는 다양한 패키지를 포함한다.( **dplyr**, **ggplot2**,
    **haven** 등)

#### 데이터 불러오기(diamonds)

``` r
data(diamonds)
diamonds %>% head()
```

    ## # A tibble: 6 x 10
    ##   carat cut       color clarity depth table price     x     y     z
    ##   <dbl> <ord>     <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
    ## 1 0.23  Ideal     E     SI2      61.5    55   326  3.95  3.98  2.43
    ## 2 0.21  Premium   E     SI1      59.8    61   326  3.89  3.84  2.31
    ## 3 0.23  Good      E     VS1      56.9    65   327  4.05  4.07  2.31
    ## 4 0.290 Premium   I     VS2      62.4    58   334  4.2   4.23  2.63
    ## 5 0.31  Good      J     SI2      63.3    58   335  4.34  4.35  2.75
    ## 6 0.24  Very Good J     VVS2     62.8    57   336  3.94  3.96  2.48

-   총 10개의 변수로 구성되어있다.

#### 데이터 불러오기(mpg)

``` r
data(mpg)
mpg %>% head()
```

    ## # A tibble: 6 x 11
    ##   manufacturer model displ  year   cyl trans      drv     cty   hwy fl    class 
    ##   <chr>        <chr> <dbl> <int> <int> <chr>      <chr> <int> <int> <chr> <chr> 
    ## 1 audi         a4      1.8  1999     4 auto(l5)   f        18    29 p     compa~
    ## 2 audi         a4      1.8  1999     4 manual(m5) f        21    29 p     compa~
    ## 3 audi         a4      2    2008     4 manual(m6) f        20    31 p     compa~
    ## 4 audi         a4      2    2008     4 auto(av)   f        21    30 p     compa~
    ## 5 audi         a4      2.8  1999     6 auto(l5)   f        16    26 p     compa~
    ## 6 audi         a4      2.8  1999     6 manual(m5) f        18    26 p     compa~

-   총 11개의 변수로 구성되어있다.
-   **displ** 변수는 자동차 엔진의 크기
-   **hwy** 변수는 연료 효율성 (miles per gallon)

geom\_point 활용
----------------

그래프 상에 점들을 출력하는 함수

#### 산점도 그리기

**displ** 를 x축,**hwy** 를 y축

``` r
ggplot(data=mpg)+
geom_point(mapping = aes(x = displ, y = hwy))
```

![](/assets/images/geom_point1-1.png)
