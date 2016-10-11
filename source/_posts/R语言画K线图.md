---
title: R语言画K线图
date: 2016-10-10 14:48:36
tags:
---

k线图是用的最多的一种图，可以把价格很好的呈现出来。

## 获取数据 ##

利用quantmod包，从互联网下载股票数据

```{r}
library(quantmod)

stock <- getSymbols("AAPL",from="2016-01-01",auto.assign=FALSE)
head(stock)

names(stock) <- c("open","high","low","close","volume","adjusted")
head(stock)
```

## 画简单的k线图 ##

```{r}
chartSeries(stock)
addMACD()
```

非常简单的2个函数，就可以实现股票数据的可视化。
但是，这个功能是封装好的通用的函数，如果我们要自定策略模型，就需要自己写代码来实现。
比如，自定义的支持量化机(SVM)分类器模型。


## 自定义K线图 ##

利用ggplot2中关于绘制boxplot的方法，可以画出K线图。把box的lower设为开盘价，upper设为收盘价，middle设为NA，上须取成最高价，下须取成最低价。

```{r}
library(ggplot2)
stock <- data.frame(stock)
stock$date <- rownames(stock)
stock$candleUp <- pmax(stock$open, stock$close)
stock$candleDown <- pmin(stock$open, stock$close)
stock$candleMiddle <- NA
stock$color <- ifelse(stock$close<stock$open,"green","red")

head(stock)

p <- ggplot(stock)

p + geom_boxplot(aes(lower=candleDown, middle=candleMiddle, upper=candleUp, x=date, ymax=high, ymin=low))

p + scale_fill_manual(values=c('green','red'))+guides(fill=FALSE)

p

```


