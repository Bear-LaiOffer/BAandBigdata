# 大数据金融商业分析成果展示

Lending Club 创立于2006年，主营业务是为市场提供P2P贷款的平台中介服务，公司总部位于旧金山。公司在运营初期仅提供个人贷款服务，至2012年平台贷款总额达10亿美元规模。
2014年12月，Lending Club在纽交所上市，成为当年最大的科技股IPO。

2014年后公司开始为小企业提供商业贷款服务。
2015年全年Lending Club平台新设贷款金额达到了83.6亿美元。

目前，全世界的P2P热潮开始降温，但市场已经逐渐成熟化。


![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/p2panalysis.jpg)


## 项目名称:
预测Lending Club的贷款风险

## 项目目的：
学习P2P公司的风控计划和用数据分析的办法衡量未来的贷款风险，降低坏账。

## Reports:
贷款数据包括以下的特征：

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/2.jpg)

贷款的主要时间
![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/dateissued.png)

如果按照分类来看

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/dateissuedbyStates.png)

Object基类对象的数据分布情况

从图表中可以得到部分信息：

1.贷款共7个等级，占比最多的是B级
![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/loanamountbygrade.png)

2.还款的形式有两种，占比最多的是36个月

3.贷款人中大多数人工龄10+年

4.贷款人的房屋状况大多是抵押贷款

5.大多数人贷款的目的是债务整合
![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/why.png)

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/density.png)

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/loan.png)

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/percentageof.png)


#### 空值、异常值处理

数据清洗。

原始数据集存在异常值情况，如特征变量emp_length（工龄）数据中包含‘n/a’的数据
除去异常值，还包括对空值的处理，对于较为重要的特征来说，如果缺失值占比较小，可以通过填补均值进行处理。


#### 特征筛选
清除一些与最终预测完全无关的变量，这里包括一部分方差值很小、无法得到更多信息的变量。

这里的筛选标准如下：

1.与最终研究目的无关的特征

2.方差值太小，无法获取有用信息的特征

3.不可解释的特征


#### 相关系数：研究变量之间线性相关程度的量

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/payment.png)

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/purpose.png)

数值型特征与贷款等级的相关系数分布


#### 相关性分析
其中total_rec_int（目前为止收到的利息）

bc_util（银行卡流动余额与信贷限额比率）

acc_open_past_24mths（过去24个月内的交易量）

open_il_12m（过去12个月内开设的分期付款帐户数）等特征与贷款等级呈正相关关系。

total_rev_hi_lim（总的周转信用额度）

total_rec_prncp（迄今收到的本金）
 
mths_since_recent_bc（自最近银行卡帐户开立以来的几个月）等特征与贷款等级呈明显的负相关关系。





