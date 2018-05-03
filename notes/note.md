# 99 Bottles of OOP Notes

## 1. Rediscovering Simplicity

### 1.1. Simplifying Code

"99 Bottles of Beer" 的四种不同的解决方案。

2 个极端：过于具体，过于抽象。需要从中寻找平衡。

1.1.1 方案一，不可思议的简洁 (或者说是令人费解的简洁)，代码行数最少，但在代码一致性 (consistency)，重复性 (duplication)，命名 (naming) 上有很多不足。

来帮助评估代码质量的几个问题：

1. How difficult was it to write? (过去)
1. How hard is it to understand? (现在)
1. How expensive will it be to change? (将来)

1.1.2 方案二，Speculatively General，不知道该怎么翻译，这个方案是一个极端，过于抽象，层次嵌套太深，导致很难一下子就理解，而且这种抽象也并没有带来方便改动的好处。

1.1.3 方案三，Concretely Abstract (具体地抽象?)，方法拆得过于细，过于 DRY，缺少了整体观。

实现了很多小的方法，但方法的命名不够灵活，举了一个例子，把 song 中的 beer 换成 Kool-Aid 后：

    def beer
      'beer'
    end

变成

    def beer
      'Kool-Aid' # 一种浓缩果汁冲剂
    end

首先这个方法名就已经名不副实了，相应的其它方法名 `bottles_of_beer`, `buy_new_beer` ... 也都变得说不通了。

这里作者提供了一个命令方法名的一条建议：

> You should name methods not after what they do, but after what they mean, what they represent in the context of your domain.

根据这个方法的意义，而不是行为来进行命名。

根据这条建议，下面这个方法名就不合适

    def beer
      'beer'
    end

beer 是 beer，人们不会这么说，人们会说 beer 是一种可以喝的东西，或者说 beer 是一种饮料，所以这个方法名改成 beverage 就比较合适。

    def beverage
      'beer'
    end

这样，把 song 中的 beer 换成 'Kool-Ali'，只需要轻微地改动 beverage 方法的实现即可。

1.1.4 Shameless Green，无耻之绿? 我猜意思是说，它的实现仅仅是为了能让测试通过。

这个方案在具体与抽象间找到了平衡，虽然有一些重复代码以及 hard-code，但减少了无意义的抽象，简洁易懂，也方便改动。

针对上面三个问题的答案是：方便写，方便看懂，方便改动。


### 1.2. Judging Code

如何来评估代码的质量。

怎么来定义好的代码，好的代码不仅能工作，而且简单、易懂、表达明确、方便修改。这些都比较主观性的东西。

我们需要找到一些客观的测量维度。

目前有这样一些衡量代码质量的标准

- Souce Lines of Code (SLOC, LOC)
- Cyclomatic Complexity，条件判断越式，得分越高，说明代码越复杂
- Assignments, Branches and Conditions (ABC)
  - Assignments，指变量定义的个数
  - Branches，调用别的函数或发消息的个数
  - Conditions，条件判断的个数

同理，ABC 得分越高，说明代码越复杂，越不好懂。用来计算 ABC 的工具之一 - Flog。

通过这些得分的比较，得出上面四种方案中，第四种方案，ABC 的值是四种里面最低的，而且 SLOC 也仅比第一种方案高，因此它是最优解。

### 1.3. Summary

宁可要一点点重复，也不要错误的抽象。
