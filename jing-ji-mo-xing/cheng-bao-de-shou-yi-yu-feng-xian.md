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
* ΔaccSPS :  $$\frac{coverage}{sToken.totalSupply}$$  该保单导致的accSPS增量——在保单注销的时候，它会累积到accSPSDown中；
* latestProviderIndex: 为该保单承保的承保人中，最后加入资金池的一个，可与前文的latestUnfrozenIndex结合理解

## 保单生成与利润捕获

一张保单生成的同时，设定投保人为其支付的保费为fee，则有：

$$
accRPS += \frac{fee*0.95}{sToken.totalSupply}
$$

​资金池中的每位承保人，此时的可提取收益计算公式为：

$$
reward = sTokenAmount*accRPS - RDebt
$$

​收益可以立即提取到承保人自己的钱包。

## [保单生成与资本冻结](cheng-bao-de-shou-yi-yu-feng-xian.md#bao-dan-sheng-cheng-yu-zi-ben-dong-jie)

一张保单生成的同时，accSPS也随之增加：

$$
accSPS += \frac{coverage}{sToken.totalSupply}
$$

​资金池中的每名承保人，累计资本冻结金额为：

$$
shadow = sTokenAmount*accSPS - SDebt
$$

## ​理赔资金池不足以赔付的情况

当理赔资金池的资金不足以对实际发生风险的保单进行赔付时，协议将按照承保人sToken的权重，由承保人分担理赔金额的超出部分。

这是一种极其罕见的特殊情况，并将导致兑换系数η的降低。

假设在$$η = η_0$$​时出现了这种情况。赔款超出理赔资金池部分为δ，则新的η变为：

$$
Δη = \frac{η_{0}*sToken.totalSupply - δ}{η_{0}*sToken.totalSupply}
$$

$$
η_{new} = η_{0}*Δη
$$

​可见η的值进一步降低，此后加入资金池的承保人，等额的Token所能获得的sToken数量——代表着捕获保费、承担保险责任的权重——是增多的。这显然是由于资金池的股本被侵蚀后，原有承保人的实际承保能力降低所致。

