# 大数据金融商业分析成果展示

Lending Club 创立于2006年，主营业务是为市场提供P2P贷款的平台中介服务，公司总部位于旧金山。公司在运营初期仅提供个人贷款服务，至2012年平台贷款总额达10亿美元规模。
2014年12月，Lending Club在纽交所上市，成为当年最大的科技股IPO。

2014年后公司开始为小企业提供商业贷款服务。
2015年全年Lending Club平台新设贷款金额达到了83.6亿美元。

## 项目名称:
预测Lending Club的贷款风险

## 项目目的：
学习P2P公司的风控计划和用数据分析的办法衡量未来的贷款风险，降低坏账。

## Reports:
贷款对象是谁：

![image](https://github.com/Bear-LaiOffer/BAandBigdata/blob/master/bigdataandBA/1.jpg)

在对数据进行处理前，我们需要对数据有一个整体的认识
lendData.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 133891 entries, 0 to 133890
Columns: 110 entries, id to total_il_high_credit_limit
dtypes: float64(86), object(24)
memory usage: 100.1+ MB

从上述的信息中可以看出：
1.133891行数据，110个特征变量
2.110个特征变量中有86个是浮点数类型，24个是Object对象。
获取到的信息还是太少，接下来可以通过下面的方法，得到数值型数据和Object基类的数据分布。
lendData.select_dtypes(include=['O']).describe().T\
    .assign(missing_pct=lendData.apply(lambda x : (len(x)-x.count())/len(x)))

筛选出object对象的对应信息，可分别得到非空值数量、unique数量，最大频数变量，最大频数，以及新添加一列特征变量missing_pct，表示值缺失的比重。



Object基类对象的数据分布情况

从图表中可以得到部分信息：
1.贷款共7个等级，占比最多的是B级
2.还款的形式有两种，占比最多的是36个月
3.贷款人中大多数人工龄10+年
4.贷款人的房屋状况大多是抵押贷款
5.大多数人贷款的目的是债务整合
6.id与desc特征的数据缺失率高达0.99，间接表明这两个特征可以删除掉。
同样可以按照这种方式对浮点型的数据进行数据预览，得到均值、标准差、四分位数以及数据的缺失比重等信息。
空值、异常值处理
得到上述的信息后，我们可以根据缺失比重进行数据的清洗。在这里按照60%的阈值删除数据。最后得到100个特征变量。
原始数据集存在异常值情况，如特征变量emp_length（工龄）数据中包含‘n/a’的数据，产生原因为公式应用的错误无法找到原值，而且占比较小，清除后剩余124947行数据。
除去异常值，还包括对空值的处理，对于较为重要的特征来说，如果缺失值占比较小，可以通过填补均值进行处理。
application_joint['il_util'] = application_joint['il_util'].replace('NaN',application_joint['il_util'].mean())

特征筛选
特征筛选在数据预处理中是很关键的一步，这一步对后序的分析、挖掘有很大的影响。
经过初步的数据清洗后，我们得到了100个特征变量，这其中包括一些与最终研究目的完全无关的变量，一部分方差值很小、无法得到更多信息的变量。虽然100个特征变量不算多，但如果去掉一些无用的特征减少数据维度，且有一定的降噪效果，那么这一步是必须要做的。
这里的筛选标准如下：
1.与最终研究目的无关的特征
2.方差值太小，无法获取有用信息的特征
3.不可解释的特征
我们的研究目的是探讨影响贷款等级的众多因素，关键特征grade代表的就是不同的贷款等级，如果想剔除与grade无关的特征，那么可以用相关系数来处理。

相关系数：研究变量之间线性相关程度的量

具体要如何处理呢？特征grade中包含A、B、C、D等七个贷款等级，做数值计算之前，需要将Object类型转换为数值类型。
#贷款等级Object类型转为数值类型
grade = lendData['grade'].replace('A',1)
grade = grade.replace('B',2)
grade = grade.replace('C',3)
grade = grade.replace('D',4)
grade = grade.replace('E',5)
grade = grade.replace('F',6)
grade = grade.replace('G',7)


转换过后，我们可以将特征中所有的数值类型的数据与贷款等级进行相关系数计算。
dicti = {}  #计算两组数的相关系数
for i in range(0,len(lendData.select_dtypes(include=['float64']).columns)):
    try:
        dicti[lendData.columns[i]] = np.corrcoef(lendData[lendData.columns[i]].dropna(),grade)[0,1]
        print(lendData.columns[i],np.corrcoef(lendData[lendData.columns[i]].dropna(),grade)[0,1])
    except Exception as e:
        continue


经过数据可视化得到下面的相关系数分布图。





数值型特征与贷款等级的相关系数分布

经过筛选后，得到54个特征（不包括未转换数据类型的其他Object类型特征），其中total_rec_int（目前为止收到的利息）、bc_util（银行卡流动余额与信贷限额比率）、acc_open_past_24mths（过去24个月内的交易量）、open_il_12m（过去12个月内开设的分期付款帐户数）等特征与贷款等级呈正相关关系。total_rev_hi_lim（总的周转信用额度）、total_rec_prncp（迄今收到的本金）、mths_since_recent_bc（自最近银行卡帐户开立以来的几个月）等特征与贷款等级呈明显的负相关关系。
同时，我们注意到这样的一个问题，在相关系数的分布中，有一部分相关系数较高的特征是由贷款等级来确定的（比如说贷款总金额、未偿还的本金、迄今收到的本金、利息等等，都是确定贷款等级之后才有的信息），而不是决定贷款等级的因素，因果关系不成立。这样的特征即使于贷款等级相关性高，也与最终的目的无关。
相关系数只是筛选的一种标准，具体的特征留存还需要根据对贷款业务的理解，有所保留的删减特征。
之后我们对上述的54个特征进行方差筛选，对于方差值较小、变化幅度较小的特征进行剔除，当然要综合考虑。
from sklearn.feature_selection import VarianceThreshold
#方差选择法，返回值为特征选择后的数据 #参数threshold为方差的阈值
lend = VarianceThreshold(threshold=2).fit_transform(lendData.select_dtypes(include=['float64']))






特征、相关系数、方差信息一览图

针对上述的特征进行进一步的方差筛选。其中特征collections_12_mths_ex_med无法解释/与研究目标无关，delinq_2yrs、acc_now_delinq很重要，其余特征无法判断，先保留看看。
特征重要性
经过初步特征筛选后，我们发现相关系数因素有些单一，并不能确定哪个特征更为重要，更需要进行深度探索。经过搜索得知GBDT算法可以算出变量的重要性。因为lending club贷款数据中并不包含“分类”变量target，所以GBDT通用的特征选择方法无法使用。
经过搜索找到了造好的轮子（取个巧），直接得到了算法计算后的结果，如下图所示。







其中dti（借款人每月已还债务总额占总债务计算的比率）、bc_util（所有银行卡账户的总流动余额与信贷限额/信用额度的比率）、mo_sin_old_rec_ti_op（自最早的周转帐户开立以来的月份）等特征较为重要。
Tip： 综上结合相关系数与特征重要性，去掉无因果关系的、重要性较低的特征，我们得到如下的筛选后的特征。





最终特征筛选结果（按特征重要性排序）

可能大家会注意到在筛选特征的过程中只针对数值型特征进行筛选，那么Object类型的特征呢？
根据前面得到的信息，共有24个Object类型的特征，其中有大部分特征是贷款后的才有的信息，并不能决定贷款等级。而且，在查看特征重要性中已包括Object类型的特征，如home_ownership（房屋所有权状态，包括租赁、拥有、贷款抵押三种类型的值），其余特征并不在考虑范围内。

数据的前期处理部分就到这里了，下一篇文章将主要对数据进行可视化分析、结论总结等。其实大部分的工作都在数据处理部分，可视化占较少的一部分时间。处理好了数据对后续的工作有很大的影响。

