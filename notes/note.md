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

## 2. Test Driving Shameless Green

### 2.1. Understanding Testing

TDD 的流程：

1. Write a test
1. Make it run
1. Make it right

即 Red/Green/Refactor。

### 2.2. Writing the Frist Test

"don't overthink it"，不要过度思考，意思大概是说先不要过早优化吧。

一个 test 的三部分：

1. Setup
1. Do
1. Verify

### 2.3. Removing Duplication

去除了 if 的使用。略。

### 2.4. Understanding Transformations

变形 (?) - 一种改变代码行为的简单操作，由 Martin 所定义。

constant -> scalar ("replacing a constant with a variable or an argument")，这种变形排第四优先级。

unconditional -> if ("splitting the execution path")，这种变形排第六优先级。

在 2.3. 示例了，我们对 `verse(num)` 方法先后采用了加 if，使用插值的变形。

### 2.5. Tolerating Duplication

为了更好地理解，允许容忍少量的重复。过度的抽象，会增加理解代码的难度。

### 2.6. Hewing to the Plan

在 `verse(num)` 方法中，当条件增加时，用 case 替代 if。

这里说道，case 和 if...elsif 隐含的意义是不一样的。使用 case 对读者更友好。

### 2.7. Exposing Responsibilities

考虑 `verses(a, b)` 方法的实现，内部应该用 `verse(num)` 来实现它。

Green Bar Patterns:

1. Fake it ("Til You Make It")
1. Obvious Implementation
1. Triangualte

### 2.8. Choosing Names

将 `verses(a, b)` 重命名成 `verses(starting, ending)`，使参数意义更明确。

增加方法 `song`

    def song
      verses(99, 0)
    end

为什么给 Bottles 增加一个 `song` 的方法，而不是直接使用它的 `verses(99, 0)` 方法呢，因为后者需要调用者知道太多信息：

1. 方法的名字 verses
1. 它需要两个参数
1. 第一个参数表示起始数
1. 第二个参数表示结束数
1. 起始数是 99
1. 结束数是 0


### 2.9. Revealing Intentions

diffference between intention and implementation.

此例中，`song` 方法是 intention，`verses(99, 0)` 是 implementation。implementation 是真正的实现，是 detail。

到目前为止，`song` 还没有测试，违反了 TDD。

### 2.10. Writing Cost-Effective Tests

大概意思是说，测试和代码不能太耦合。下面的小节会来继续说这个问题。

### 2.11. Avoiding the Echo-Chamber

为 `song` 方法写测试，第一个版本

    def test_the_whole_song
      bottles = Bottles.new
      assert_equal bottles.verses(99, 0), bottles.song
    end

用方法自己的实现来测试自己，这是一种错误的测试方法，也是上小节所说的，测试和实现代码耦合的现象。

测试代码的输出，不能依赖于代码自身的实现，不然这就形成了耦合。测试代码的输出必须是 hard-code 的。

song 方法的测试，不应该去知道 song 内部具体是怎么去实现的。

在代码实现中去做 DRY，但不要在测试中做 DRY。

### 2.12. Considering Options

> DIR is a very good idea in code, but much less useful in tests. When testing, the best choice is very often just to write it down.

### 2.13. Summary

测试，做得好的话，可以加速你的开发效率和降低开发成本，但是，如果错误的测试，则起到相反的作用。
