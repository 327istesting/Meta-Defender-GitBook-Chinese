# 承保与杠杆挖矿

## 承保的概念

如果你对Meta Defender白名单内的某个项目安全性有充分的信心，可以通过为它的保险资金池注入资本，成为它的承保人。

承保人根据自身提供的资本占整个资本池的权重，从每一笔保费中分得自己应得的收益。同时，对应地冻结承保资本，直至保单到期。

保费收入的5%作为Meta Defender的项目收入，其余部分全部归属于承保人。

## 流动性的提供与撤出

作为一个去中心化金融协议，流动性进出的自由性、无审查性是必要的条件。但保险资本，又与普通的DeFi流动性有本质上的区别。

* 1、保险资本对保费的捕获是即期事项；而赔付与否，是远期事项。
* 2、保险资本对保费的捕获是确定事项，而赔付与否，是不确定事项。

很显然，承保人如果承保了保单，就要冻结相应的保额。在这笔保单到期之前，只能撤出自身提供资本的未冻结部分。

协议在确保偿付资本充足的前提下，允许承保人自由地撤出未被冻结的资本。已冻结的部分，在保单到期、资本解冻后，也可以撤出。

## 为承保资本加上杠杆

区块链保险目前是一个全新的市场，用户教育远未达到传统保险市场的成熟度。保费收益在早期可能会低于同期其他DeFi协议的收益率，降低承保人的参与意愿。同时，如果理赔事件没有发生（显然谁都不希望它发生），承保资金长期闲置在协议里会造成巨额的资本浪费。

Meta Defender协议对于承保资本进行二次运用，存量资金的70%参与公链官方的安全矿池，挖矿收益按照如下比例分配：

| 收益方                | 比例  |
| ------------------ | --- |
| Meta Defender代币持币人 | 10% |
| 承保人                | 70% |
| 理赔资金池              | 20% |

{% hint style="info" %}
该表中的第一项和第三项，可以随着项目的去中心化治理而调整，目的在于兼顾持币人的利益和协议的金融安全。
{% endhint %}

当理赔发生时，优先使用理赔资金池中的资金赔付；不足以赔付时，由全体承保人按照提供资本的权重，分摊超额部分。

杠杆挖矿不仅提高了项目资本的利用率和承保人的收益率，也保护了Meta Defender协议的安全。它尽可能地避免了保险协议的股本被侵蚀，随着时间的推移，理赔资金池累积，协议的保险能力将越来越强。
