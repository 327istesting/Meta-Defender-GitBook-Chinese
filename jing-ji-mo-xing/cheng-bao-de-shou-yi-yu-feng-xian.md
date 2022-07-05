# 承保的收益与风险

为了便于计价，Meta Defender建议使用稳定币进行投保与承保。假定该稳定币名为Token。承保人向某个保险资金池提供数量为A的token，根据该资金池的资本兑换率η，获得sToken（staked token）数量为：

$$
A' = \frac{A}{η} （η\leq1）
$$

sToken并非可转让的ERC20 token，​它只是资金池对承保人的一次记账，作为捕获保费收益、未来提取资本的凭据。

在每个资金池建立的时候，η的起始值都为1。鉴于理赔事项优先通过挖矿结余的理赔资金池支付（详情见：[tou-bao-li-pei-yu-zhi-li-wa-kuang.md](../xiang-mu-jia-gou/tou-bao-li-pei-yu-zhi-li-wa-kuang.md "mention")），只要没有发生大规模理赔事件导致侵袭资金池本金，η的值都维持为1。

## 全局变量

* η：Token 与 sToken 的兑换汇率；
* accumlatedRewardPerShare（accRPS）：1 sToken 累计捕获的保费收益；
* accumulatedShadowPerShare（accSPS）： 1 sToken 累计“捕获”的风险；
* accumulatedShadowPerShareDown（accSPSDown）：保单到期注销导致的accSPS释放（调减）；

{% hint style="info" %}
没有人想“捕获”风险。但如果你分得了保费，就要承担相应份额的风险。我们可以打一个物理学上的比喻：风险（shadow）是一种反物质代币，代表着未来可能发生的资本损失（虽然绝大情况下及时发生损失，也被理赔池消化），它的存在限制了承保人撤出资本的额度。
{% endhint %}

* latestUnfrozenIndex：最新注销的一张保单，为其承保的承保人中，加入资金池最晚的一个。或者用更易理解的语言表述：“最年轻的一个”（年龄=加入资金池的时长）

## 承保人与保单的数据结构

当一个承保人stake一些Token加入协议，协议会这样标记他：

* index：承保人加入协议的次序；
* sTokenAmount：承保人获得的sToken数量（即前文中的A’）；
* rewardDebt（RDebt）：$$sTokenAmount * accRPS$$​ 计算承保人捕获收益时的调减系数；
* shadowDebt（SDebt）：$$sTokenAmount * accSPS$$​ 计算承保人“捕获”风险时的调减系数；

当一张保单生成的时候，协议会这样标记它：

* coverage：保单保额，也就是将被所有承保人分担的shadow；
* ΔaccSPS : 该保单导致的accSPS增量——在保单注销的时候，它会







