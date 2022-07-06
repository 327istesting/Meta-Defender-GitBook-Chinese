# 承保资本退出机制

## 承保人可以撤出的资本

在承保人的资本未被源源不断生成的保单们完全冻结之前，他们可以随时撤出部分空闲的资本，而已冻结的部分需要等待相应的保单到期并注销，完成解冻。

承保人可以撤出的资本，计算公式为：

$$
withdraw = η*sTokenAmount - shadow
$$

​shadow是承保人被冻结的资本的额度。它的基础算法见上一章节中的[#bao-dan-sheng-cheng-yu-zi-ben-dong-jie](cheng-bao-de-shou-yi-yu-feng-xian.md#bao-dan-sheng-cheng-yu-zi-ben-dong-jie "mention")。但如果考虑已到期的保单会释放出部分资本，情况会变得更复杂。

## 保单的注销

无论是到期失效的保单，还是完成赔付的保单，都应释放相应的承保人资本。即使是承保资金池的资金被侵蚀的情况下。

在保单生成的时候，我们记录了它的lastProviderIndex和ΔaccSPS。当它被注销的时候，协议将[#quan-ju-bian-liang](cheng-bao-de-shou-yi-yu-feng-xian.md#quan-ju-bian-liang "mention")中的 latestUnfrozenIndex同步为这张保单的lastProviderIndex，并累积accSPSDown：

$$
accSPSDown += ΔaccSPS
$$

​计算一个承保人的shadow时，需要先行判断他的index与latestUnfrozenIndex的大小关系。当index<=latestUnfrozenIndex时，表明正在被释放的资本中，已经有该承保人当初被冻结的部分。他的shadow计算公式就变为：

$$
shadow_{down} = sTokenAmount*accSPSDown - SDebt
$$

$$
shadow_{new}=shadow-shadow_{down} = sTokenAmount*(accSPS-accSPSDown)
$$

$$
withdraw = η*sTokenAmount - shadow_{new}
$$

## 承保人撤出资本后

为了使协议便于维护、降低用户调用合约时的gas成本，Meta Defender目前要求撤资的承保人一次性撤走全部的剩余流动性（即上文的withdraw）。

当然，可能您只想“部分撤出”。实现这个功能异常简单，您只需要先全部撤出，再以您期望的承保金额重新加入。这可以使协议免于维护一个巨大的账本，否则gas费用将变得难以想象。

承保人撤出资本时，需要销毁自己所有的sToken。
