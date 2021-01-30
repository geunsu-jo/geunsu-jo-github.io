---
layout: single   
title: "[시계열 분석]R에서 이동평균법과 지수평활법"   
excerpt: "이동평균법과 지수평활법을 소개하고 R을 활용하여 실습을 진행합니다."   
tags:
  - time series
  - R
comments: true
toc: true
toc_sticky: true
categories:
  - time series   
use_math: true   
output:
  md_document:
    variant: markdown_github
    preserve_yaml: true
---



<br>

단순이동평균법
--------------

``` r
data("AirPassengers")
library(forecast)
m3=filter(AirPassengers, filter=rep(1/3,3), sides=1)
m6=filter(AirPassengers, filter=rep(1/6,6), sides=1)
m12=filter(AirPassengers, filter=rep(1/12,12), sides=1)

par(mfrow=c(2,2))
plot(AirPassengers, main="AirPassengers: original data")
plot(m3, main="3-point moving average")
plot(m6, main="6-point moving average")
plot(m12, main="12-point moving average")
```

![](/assets/images/time_series/smoothing/by_order-1.png)

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
ff=filter(dd1.ts, filter=rep(1,4)/4, method="convolution", sides=1)
plot(dd1.ts, main="dd1.ts with simple moving average smoothing")
lines(ff, col="red", lty=2, lwd=2)
abline(h=mean(dd1), col="blue")
```

![](/assets/images/time_series/smoothing/sma-1.png)

``` r
res=ff[-1:-3,]-dd1.ts[-1:-3,] #remove first three values which are not able to be predicted.
tsdisplay(res, main="Residuals by MA(4) for dd1")
```

![](/assets/images/time_series/smoothing/sma_acf-1.png)

``` r
Box.test(res, type="Box-Pierce") # H0: TS is independent.
```

    ## 
    ##  Box-Pierce test
    ## 
    ## data:  res
    ## X-squared = 0.97729, df = 1, p-value = 0.3229

### 이중

``` r
ff1=filter(dd1.ts, filter=rep(1,3)/3, method="convolution", sides=1)
ff2=filter(ff1, filter=rep(1,3)/3, method="convolution", sides=1)
plot(dd1.ts, main="dd1.ts with simple moving average smoothing")
lines(ff2, col="red", lty=2, lwd=2)
abline(h=mean(dd1), col="blue")
```

![](/assets/images/time_series/smoothing/dma-1.png)

``` r
res=ff2[-1:-3,]-dd1.ts[-1:-3,]
tsdisplay(res, main="Residuals by double MA smoothing")
```

![](/assets/images/time_series/smoothing/dma_acf-1.png)

``` r
Box.test(res, type="Box-Pierce") # H0: TS is independent
```

    ## 
    ##  Box-Pierce test
    ## 
    ## data:  res
    ## X-squared = 1.0548, df = 1, p-value = 0.3044

### ma를 이용한 double movin avg

``` r
mm4_1 <- ma(dd1.ts, order=4, centre=F) #MA(4) without a center

mm3_1 <- ma(dd1.ts, order=3, centre=T) #MA(3) with a center
mm3_2 <- ma(mm3_1, order=3, centre=T) #double MA(3) with a center

res3 <- mm3_2 - dd1.ts
Box.test(res3)
```

    ## 
    ##  Box-Pierce test
    ## 
    ## data:  res3
    ## X-squared = 1.3138, df = 1, p-value = 0.2517

``` r
f_simple <- forecast(mm3_1, h=2)
f_double <- forecast(mm3_2, h=2)

par(mfrow=c(1,2))
plot(f_simple, main="forecast simple MA(3)") # forecast
plot(f_double, main="forecast Double MA(3)") # forecast
```

![](/assets/images/time_series/smoothing/pred_ma-1.png)

``` r
f_double
```

    ##         Point Forecast    Lo 80    Hi 80    Lo 95    Hi 95
    ## 2010 Q3       1331.444 1308.163 1354.725 1295.839 1367.049
    ## 2010 Q4       1331.444 1298.520 1364.368 1281.091 1381.797

``` r
accuracy(f_double)
```

    ##                      ME     RMSE      MAE         MPE      MAPE      MASE
    ## Training set -0.7499552 17.16946 12.23685 -0.06423849 0.9102839 0.4034125
    ##                   ACF1
    ## Training set 0.3147654

지수
----

exponential smoothing \# (beta=F, gamma=F): no trend and no seasonal
effect alpha 지정

``` r
ho=HoltWinters(dd1.ts, alpha=0.1, beta=F, gamma=F)
ho
```

    ## Holt-Winters exponential smoothing without trend and without seasonal component.
    ## 
    ## Call:
    ## HoltWinters(x = dd1.ts, alpha = 0.1, beta = F, gamma = F)
    ## 
    ## Smoothing parameters:
    ##  alpha: 0.1
    ##  beta : FALSE
    ##  gamma: FALSE
    ## 
    ## Coefficients:
    ##       [,1]
    ## a 1334.816

``` r
head(ho$fitted, 10) #fitting value
```

    ##             xhat    level
    ## 2006 Q2 1342.000 1342.000
    ## 2006 Q3 1352.000 1352.000
    ## 2006 Q4 1342.000 1342.000
    ## 2007 Q1 1342.100 1342.100
    ## 2007 Q2 1350.390 1350.390
    ## 2007 Q3 1351.551 1351.551
    ## 2007 Q4 1361.996 1361.996
    ## 2008 Q1 1352.996 1352.996
    ## 2008 Q2 1341.997 1341.997
    ## 2008 Q3 1343.697 1343.697

alpha지정x

``` r
ha=HoltWinters(dd1.ts, beta=F, gamma=F) #exponential smoothing
ha
```

    ## Holt-Winters exponential smoothing without trend and without seasonal component.
    ## 
    ## Call:
    ## HoltWinters(x = dd1.ts, beta = F, gamma = F)
    ## 
    ## Smoothing parameters:
    ##  alpha: 6.610696e-05
    ##  beta : FALSE
    ##  gamma: FALSE
    ## 
    ## Coefficients:
    ##       [,1]
    ## a 1341.998

``` r
head(ha$fitted, 10)
```

    ##             xhat    level
    ## 2006 Q2 1342.000 1342.000
    ## 2006 Q3 1342.007 1342.007
    ## 2006 Q4 1342.001 1342.001
    ## 2007 Q1 1342.001 1342.001
    ## 2007 Q2 1342.006 1342.006
    ## 2007 Q3 1342.008 1342.008
    ## 2007 Q4 1342.015 1342.015
    ## 2008 Q1 1342.010 1342.010
    ## 2008 Q2 1342.004 1342.004
    ## 2008 Q3 1342.005 1342.005

``` r
plot(ha)
```

![](/assets/images/time_series/smoothing/es-1.png)

``` r
fa <- forecast(ha, h=4)
fa
```

    ##         Point Forecast    Lo 80    Hi 80    Lo 95    Hi 95
    ## 2011 Q1       1341.998 1220.576 1463.421 1156.299 1527.698
    ## 2011 Q2       1341.998 1220.576 1463.421 1156.299 1527.698
    ## 2011 Q3       1341.998 1220.576 1463.421 1156.299 1527.698
    ## 2011 Q4       1341.998 1220.576 1463.421 1156.299 1527.698

``` r
fa$fitted
```

    ##          Qtr1     Qtr2     Qtr3     Qtr4
    ## 2006       NA 1342.000 1342.007 1342.001
    ## 2007 1342.001 1342.006 1342.008 1342.015
    ## 2008 1342.010 1342.004 1342.005 1342.010
    ## 2009 1342.004 1341.994 1342.003 1342.002
    ## 2010 1342.006 1342.001 1341.997 1342.007

``` r
fa$residuals
```

    ##              Qtr1         Qtr2         Qtr3         Qtr4
    ## 2006           NA  100.0000000  -90.0066107    0.9993394
    ## 2007   82.9992733   19.9937865  113.9924647  -70.0150709
    ## 2008  -99.0104425   16.9961028   69.9949793  -89.0096479
    ## 2009 -141.0037637  136.0055576  -20.0034333   63.9978890
    ## 2010  -88.0063417  -53.0005238  155.0029799 -134.0072669

``` r
tsdisplay(fa$residual)
```

![](/assets/images/time_series/smoothing/es_acf-1.png)

``` r
Box.test(fa$residual, type="Box-Pierce")
```

    ## 
    ##  Box-Pierce test
    ## 
    ## data:  fa$residual
    ## X-squared = 2.0761, df = 1, p-value = 0.1496

``` r
plot(fa)
```

![](/assets/images/time_series/smoothing/es_pred-1.png)
