---
title: cws/sp/ap 感知器
date: 2017-09-12 16:38:06
tags:
---

# CWS SP AP #

这些是什么东西。

先参考 张神的 代码 [我是代码链接](https://github.com/zhangkaixu/minitools/blob/master/cws.py)，
这个页面有CWS和AP(average perceptron，平均感知机)，以及听到的SP(structural perceptron，结构化感知机)，它们好像都在说同一个东西。
我Google了一下，发现很难搜到与CWS里代码相关的average perceptron的内容。
以下还是依张神的称呼，叫这个代码片中涉及到的模型为CWS。

CWS，用于序列标注，看起来是和CRF、HMM类似的模型。
既然是序列标注用模型，那我首先从模型的输入说起。

在张神的代码里，模型接受文字序列作为输入，由gen_features生成特征。
gen_features 包含七个特征：

`
    features=['1'+mid,'2'+left1,'3'+right1,
              '4'+left2+left1,'5'+left1+mid,'6'+mid+right1,'7'+right1+right2]
`

显而易见，这是上下文特征窗口。
这七个特征，与当前字符的词结构标记（BMES）共同构成，CWS的七个key。

每个key，对应这一个特征的概率 `p(f_j|y_i)` 。这么说起来，CWS反而有点像ngram模型了。

对于CWS模型，还要学习另一类特征：
状态转移概率

```
    def update(self,x,y,delta): # 更新权重
        # ***
            self.weights.update_weights(str(y[i])+':'+str(y[i+1]),delta)
```

代码115行，给出了状态转移概率 `p(y_i+1|y_i)` 的初始化和更新方法。
张神的代码，为了方便管理，把两个概率，都按照 ` key:value ` 对的结构组织。

整个CWS模型，即是由以上两个概率构成。

## 前向计算、解码

对于序列标注任务，前向计算，需要计算所有可能序列的概率。
解码时，再从中选择最优序列。

代码中的decode函数，即是依据动态规划的方法实现了这一部分。

## 反向传播/在线更新算法

模型总是要训练的。
CWS模型由于其特性，天然可以支持在线更新算法。

CWS的反向传播，即：

对于给定的输入样本 x, 其样本真实边界分布为y，其模型解码输出为z。
如果y与z不相等，则需要对y,z涉及的特征分别进行奖励和惩罚。
在张神的代码中，奖励与惩罚的步长均为1。

```
    update_weights(x, y, delta)
```

也就是说，由x,y生成模型的特征输入，并对这些特征进行奖励。
由x,z生成另一组输入特征，对这些特征进行处罚。

那实际上，只更新一个步长，并不能真的让，x输入模型时，得到y的解码结果。
只是向拟合靠近了一步。
此外，需要通过一定方式控制更新幅度。

更新幅度与步长并不一致，但步长决定了更新的方向。
具体的幅度由penalty函数确定，比如直接更新(no_regu)，或者l1 l2正则的方法。

在一次完全更新（训练集）之后，需要对历史更新进行平均。
张神代码里，是这么搞的。对于no_regu，每次累积时， acc += dstep * value。
其中dstep，是上次更新样本与当前更新的样本，中间的相差的样本数。

不仅是no_regu，还有l1 l2，此处实现均有点难以理解。
稍后再解决。
```
    TODO: Why use dstep for average.
```