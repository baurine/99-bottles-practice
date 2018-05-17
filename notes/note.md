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

## 3. Unearthing Concepts

新需求，将 "6 bottles" 改成 "1 six-pack"。

### 3.1. Listening to Change

> The most cost-effective code is as good as necessary, but no better.

不要过早优化，那样会消耗你宝贵的时间。

最好是随着需求变化，逐步地优化你的代码。

我们还按照 Shameless Green Solution 的思路来改造代码，以满足 "1 six-pack" 的需求，但这时候，会发现，在 `verse(num)` 方法中，重复的东西实在是太多了，已经达到了不可接受的地步。

### 3.2. Starting With the Open/Closed Principle

设计模式的几个原则：SOLID

- S - Single Responsibility
- O - Open/Closed. Open to extension, closed to modification
- L - Liskov Subsitution. Subclass should be subsitutable for their superclass
- I - Interface Segregation. Objects should not be forced to depend on methods they don't use.
- D - Dependency Inversion. Depend on abstraction, not on concretions.

后面三个基本讲的差不多是一个事情，依赖抽象而不是具体。

### 3.3. Recognizing Code Smells

Code Smells - 不好的代码。

### 3.4. Identifying the Best Point of Attack

找到改造代码的最好入手点，此例中，无疑是 `verse(num)` 方法的 `case` 表达式，在此处包含了很多重复。

### 3.5. Refactoring Systematically

> Refactoring is the process of changing a software system in such a way that it doesn't alter the external behavior of the code yet improves its internal structure.

重构步骤：

- First, you rearrange existing code so that it becomes open to the new requirement.
- Next, you write new code to meet that requirement.

第一步就叫重构。

每重构一步，就跑一次测试，以保证你的重构没有破坏 external behavior。

### 3.6. Following the Flocking Rules

Flocking - 鸟群

Flocking Rules:

1. Select the things that are most alike
1. Find the smallest difference between them
1. Make the simplest change that will remove the difference

### 3.7. Covering on Abstraction

利用 Flocking Rules 去改造 `verse(num)` 方法，使之减少重复，更抽象。

#### 3.7.1. Focusing on Difference

> Diffrerence represents a smaller abstraction within the larger one. If you can name the difference, you've identified that smaller abstraction.

在 `verse(num)` 方法中，`case 2` 和 `else` 逻辑是相同的，仅仅的区别是 `bottle` 和 `bottles` 一处。

#### 3.7.2. Simplifying Hard Problems

修改 `case 2` 的逻辑，去掉 hard-code，最终它与 `else` 的不同就只有一处了。

    when 2
      # ...
      "#{number - 1} bottle of beer on the wall.\n"
    else
      # ...
      "#{number - 1} bottles of beer on the wall.\n"
    end

#### 3.7.3. Naming Concepts

"bottle / bottles"，表面上看，它们表示 bottle 的单复数形式，但它们真正的意义并不是 "pluralization"。

所以，下面的抽象是不对的。

    def pluralization(number)
      if number == 1
        'bottle'
      else
        'bottles'
      end
    end

更何况，当 number 为 6 时，后面跟的应该是 six-pack，即不是 bottle，也不是 bottles。

所以，把 bottle/bottles/six-pack，更应该理解是某种单位，在这里，理解成容器 container 更合适。

#### 3.7.4. Making Methodical Transformations

这一小节主要想表达的是，重构时，最好一小步一小步来，最好是改一行就重新测试一下，没有错误后再继续。不要一下子同时重构太多。(步子迈得太大，容易扯着蛋...)

呵呵，这也太谨慎了吧... (我有点不能接受)

#### 3.7.5. Refactoring Gradually

这一小节逐步演示重构的过程，`container(num)` 方法是如何一步一步形成的。

最后的版本：

    def container(num)
      if num == 1
        'bottle'
      else
        'bottles'
      end
    end

### 3.8. Summary

这一章，并没有实际解决 six-pack 的问题，只是准备阶段，为了满足 six-pack 的需求，需要把原来的代码重构成 open to external。

## 4. Practicing Horizontal Refactoring

这一小节继续使用 Flocking Rules 来改进代码。

### 4.1. Replacing Difference with Sameness

处理 `when 0` 和 `when 1` 的情况。

把 `1 bottle` 和 `n bottles` 统一优化成 `"#{number} #{container(number)}"`

### 4.2. Equivocating About Names

处理 `it` 和 `one` 的情况。

> when concepts are fuzzier, finding a good name can be much harder.

取名就是一个相当大的难题。

本小节提供一些取名的建议。

> Names should neither be too general nor too specific. For example, `thing` is too broad, and `it_or_one` too narrow.

如果你问顾客会怎么来称呼 `it` 或 `one` 在这里表明的含义，他们会把它称为 `pronoun`，但似乎用 `pronoun` 有点太 general 了，如果要再具体一些，可以称之为 `thing_drunk`，但 `thing_drunk` 有点不可接受... 所以这里没有一个完美的名字。

当遇到这种情况时，有三种策略：

1. 一些人会徘徊几分钟，然后选择一个你觉得 good enough 的名字，之后想到更好的名字后再优化
1. 一些人会快速地选择一个毫无意义的名字，比如 foo, namethis, tmp 等，来快速地推进，继续往前。"You will never know less than you know right now."
1. 寻求别人的帮助，问问别人有什么好的主意。

在这里，`pronoun` 是一个 good enough 的选择，我们先暂时用着这个，之后如果想到更好的名字再来优化。

    def pronoun(number)
      if number == 1
        'it'
      else
        'one'
      end
    end

### 4.3. Deriving Names From Responsibilities

处理 `no more` 的情况。

'99' '50' '1' 'no more' 代表 quantity。所以可以定义一个 quantity 的方法。

### 4.4. Choosing Meaningful Defaults

用 `:FIXME` 作为参数的默认值，用来提醒用户这个地方后面还需要再修改。

    def quantity(number)
      if number == 0
        'no more'
      else
        number
      end
    end

如此重构后，统一了 `when 1` 和 `else` 的逻辑，只剩下 `when 0` 了。

### 4.5. Seeking Stable Landing Points

截止目前为止，新增加的三个方法，`quantity(num)`, `pronoun(num)`, `container(num)`，它们都符合单一职责，逻辑也相似，具有 consistency。

### 4.6. Obeying the Liskov Substitution Priciple

继续重构，找不同。

"No more" 与 "no more"，代表的意义是一样的，只是一个因为在句首，要大写，另一个在句中，不需要。

不需要一个特别的方法来区分它们，只需要当它在句首时，对它调用 `.capitalize` 来大写首字母。

但有一个问题，在 `quantity(num)` 方法的 else 分支中，返回的是数值型的 number，并不是字符串，没有 capitalize 方法。

修改 `quantity(num)` 方法，在 else 分支中使用 `number.to_s`，使该方法统一返回 string 类型。

### 4.7. Taking Bigger Steps

抽象出 `action(number)` 方法。

    def action(number)
      if number == 0
        'Go to the store and buy some more'
      else
        "Take #{pronoun(number)} down and pass it around"
      end
    end

### 4.8. Discovering Deeper Abstractions

解决在 `when 0` 中 `99` 代表的意义。从而抽象出了 `successor(num)` 方法。

    def successor(number)
      if number == 0
        99
      else
        number - 1
      end
    end

### 4.9. Depending on Abstractions

最终的 `verse(num)`：

    def verse(number)
      "#{quantity(number).capitalize} #{container(number)} of beer on the wall, " +
      "#{quantity(number)} #{container(number)} of beer.\n" +
      "#{action(number)}, " +
      "#{quantity(successor(number))} #{container(successor(number))} of beer on the wall.\n"
    end

消除了所有的 conditional statements.

### 4.10. Summary

只要遵循相同的原则，不同是程序员进行重构，最终也能得到相同的结构，只不过方法的名字可能不一样。

## 5. Separating Responsibilities

前两小节使用 Flocking Rules 减少了 `verse` 方法的重复内容。

接下来回到 six-pack 的问题上来，到目前为止，我们还没有实现代码的 open。

注：当一开始我看到作者将 container, quantity 等方法抽象到一个 BottlesNumber 的类中是，我觉得有点过度抽象，但看完整章后，我觉得这个抽象是很有道理的。

### 5.1. Selecting the Target Code Smell

#### 5.1.1. Identifying Patterns in Code

通过回答一些问题，来找出代码中的一些模式。

1. 哪些方法有相同的形状？
1. 哪些方法接受相同名字的参数？
1. 这些相同名字的参数是否代表相同的意义？
1. 如果你要给这个类添加一个 private，你会放在哪？
1. 如果要把这个类分成两块，你会从哪行把它分开？
1. ...

#### 5.1.2. Spotting Common Qualities

回答以上问题。`container(num)` `quantity(num)` `action(num)` `pronoun(num)` `successor(num)` 具有相同形状，相同参数名，参数名代表相同意义。...

`verse(num)` 中的参数 num 与上述方法参数，虽然同名，但代表的意义却不相同。

### 5.2. Extracting Classes

#### 5.2.1. Modeling Abstracting

将上述五个方法，抽象到 BottlesNumber 类中，这个类并不代表实际的物体，而是表示概念。

#### 5.2.2. Naming Classes

是叫 BottlesNumber 好，还是 ContainerNumber 好，前者更具体一些，后者更抽象，更 general 一些。

在前面命名方法时，我们举了一个例子，用 `beverage` 替代了 `beer`，前者更抽象，后者更具体。那在这里，我们是不是也应该用 ContainerNumber，而不是 BottlesNumber 呢，并不是的。

修正后的命名原则：

> while you should continue to name methods after what they mean, classes can be named after what they are.

为类命名，应该具体化，为方法命名，可以更抽象化。

#### 5.2.3. Extracting BottleNumber

为抽象出 BottleNumber 时，发现我们并没有遵循 TDD 的原则，先写测试，再写实现。这是因为，Bottles 的实现依赖于 BottleNumber，我们已经为 Bottles 写了测试了。

重构的步骤：

1. parse the new code
1. parse and execute it
1. parse, execute and use its result
1. delete the unused code

接下来讲解如何一步一步实现 BottleNumber，详略，说实话，讲得太啰嗦了...

最终的实现：

    class BottleNumber
      attr_reader :number
      def initialize(number)
        @number = number
      end

      def container
        if number == 1
          'bottle'
        else
          'bottles'
        end
      end

      def quantity
        if number == 0
          'no more'
        else
          numbrer.to_s
        end
      end

      ...

      def successor
        if number == 0
          99
        else
          number - 1
        end
      end

      ...

### 5.6. Recognizing Liskov Violations

    class Bottles
      ...

      def verse(number)
        bottle_number = BottleNumber(number)
        next_bottle_number = BottleNumber.new(bottle_number.successor)
        ...

## 6. Achieving Openness

经过前面五章的铺垫，抽象出了 BottleNumber class 后，我们终于使其有了 Open 的基础了。

### 6.1 Consolidating Data Clumps

`"#{bottle_number.quantity} #{bottle_number.container}` 这样的组合在 `verse` 方法中重复了 3 次，这让我们闻到了一些 code smell，被称之为 Data Clump code smell。

如果几个东西总是同时出现，这意味着它们可能有一些深层的意义，而我们应该对这个意义进行命名。

我们可以对上面这个组合进行一个单独的命名，但在 Ruby 中，每个对象都有一个 `to_s` 的方法，而且它不用显式地被调用，在插值表达式中的对象，会自动调用 `to_s` 方法。

因此，我们为 BottleNubmer 重写 `to_s` 方法：

    def to_s
      "#{quantity} #{container}"
    end

(但是，`to_s` 不能滥用)

新的 `verse` 方法：

    def verse(number)
      bottle_number = BottleNumber(number)
      next_bottle_number = BottleNumber.new(bottle_number.successor)

      "#{bottle_number} of beer on the wall, ".capitalize +
      "#{bottle_number} of beer.\n" +
      "#{bottle_number.action}, " +
      "#{next_bottle_number} of beer on the wall.\n"
    end

有一个小细节的地方是，把 `.capitalize` 的调用挪到了句子的最后，这样也确实更 make sense。

nice! 又简洁了很多。

### 6.2 Making Sense of Conditionals

接下来来解决 BottleNumber 中每个方法中的条件语句。

通常有两种方法来替换条件：

1. 策略模式，使用组合
1. 多态，使用继承，实现子类

这里，我们使用多态更合适。

### 6.3. Replacing Conditionals with Polymorphism

多态的意义：不同的对象去响应相同的消息，消息发送者不用关心具体的接收者是谁，只关心它们接收到消息后做什么。

#### 6.3.1. Dismembering Conditionals

在 BottleNumber 中每一个方法中的条件句中，都有一个 special case 和一个 common case，我们把 special case 抽取出来，让它们交给相应的子类来实现，BottleNumber 只处理最常见的 common case，

special case 中有 number 等于 0 和 1 两种情况，因此我们从 BottleNumber 继承两个子类 BottleNumber0 和 BottleNumber1。

    class BottleNumber
      def quantity
        number.to_s
      end

      def container
        "bottles"
      end
      ...
    end

    class BottleNumber0 < BottleNumber
      def quantity
        "no more"
      end
      ...
    end

    class BottleNumber1 < BottleNumber
      def container
        "bottle"
      end
      ...
    end

#### 6.3.2. Manufacturing Objects

抽象 BottleNumber，BottleNumber0，BottleNumber1 这些类后，面临一个新的问题，我们需要根据 number 的值还选择创建 BottleNumber0，BottleNumber1 还是 BottleNumber 类的实例对象。

这种情况就是工厂方法最适合的地方了。

我们在 Bottles 类中定义一个 `bottle_number_for(number)` 的工厂方法：

    def bottle_number_for(number)
      if number == 0
        BottleNumber0
      else
        BottleNumber
      end.new(number)
    end

(又学到了 Ruby 的一种新魔法)

(话说 `bottle_number_for(number)` 这样的方法命名怎么有种 ObjectC/Swift 的感觉呢)

如果再加上 BottleNumber1，这时有三个分支了，我们改用 case。

    def bottle_number_for(number)
      case number
      when 0
        BottleNumber0
      when 1
        BottleNumber1
      else
        BottleNumber
      end.new(number)
    end

#### 6.3.4. Making Peace With Conditionals

通过上述一步步的重构，我们发现，最原始的 Shameless 版本的 `verse` 方法中，条件分支有 4 个 (case 0,1,2 and else)，而 `bottle_number_for` 中只有 3 个 (case 0,1 and else)。

减少的那一个到哪去了呢，为什么能减少呢。这是因为，在 Shameless 版本的 `verse` 方法中，number 表示 verse number，2 是一个 special case，但 2 对于 bottle number 来说，它并不是一个 specail case。

### 6.4. Transitioning Between Types

这一小节略有些不明白，我理解 successor 的优化，但不明白它为什么违背的是里氏替换原则。

现在，BottleNumber 的 successor 方法，返回的是一个 number，我们还需要自己再去用这这个得到的值 new 一个 BottleNumber，但实际我们更希望直接得到一个新的 BottleNumber 对象，而不是数字。

每一个用到 successor 方法的地方，都必须了解这个返回值是一个数字，我们还需要把它包装成 BottleNumber 对象。这增加了依赖和重复。

如果 successor 直接返回我们想要的 BottleNumber 对象，那么就减少了依赖和重复，完美。

如果要实现 successor 直接返回 BottleNumber 对象，那必须要用到生成 BottleNumber 对象的工厂方法 `bottle_number_for`，但此时，工厂方法在 BottleNumber 之外的 Bottles 类中。我们需要它这个工厂方法挪到 BottleNumber 类中，成为它的静态方法。

此时，这个工厂方法如果还叫 `bottle_number_for`，即全称 `BottleNumber.bottle_number_for`，那就显示冗余了。每个对象都有 `to_s` 方法，但我们不会为 String 类取名 `string_to_s`，为 Hash 类取名 `hash_to_s`。

我们把这个方法简化为 `for(number)`。

    class BottleNumber
      def self.for(number)
        ...
      end
      ...

      def successor
        BottleNumber.for(number-1)
      end
    end

判断某个对象是否属于某个类的实例，可以用 `kind_of?` 方法：

    bottle_number.kind_of?(BottleNumber)

尽量使你的设计遵循里氏替换原则。

### 6.5. Making the Easy Change

终于，我们可以很方便地来实现 1 six-pack 的需求了。现在 BottleNumber 已经是对外 open 了，我们只要继续它，实现 BottleNumber6 即可。

    class BottleNumber6 < BottleNumber
      def quantity
        "1"
      end

      def container
        "six-pack"
      end
    end

但些是工厂方法还不是 open 的，所以我们修改工厂方法，在 number 等于 6 时，产生 BottleNumber6 对象：

    class BottlerNubmer
      def self.for(number)
        case number
        when 0
          BottleNumber0
        when 1
          BottleNubmer1
        when 6
          BottleNumber6
        else
          BottleNumber
        end.new(number)
      end
      ...

> make the change easy (warning: this may be hard), then make the easy change.

### 6.6. Defending the Domain

如果仅仅是为了通过测试，我们可以这样来实现 BottleNumber6：

    class BottleNumber6 < BottleNumber
      def to_s
        "1 six-pack"
      end
    end

虽然一样达到了效果，但这种实现是不好的，它破坏了这个方法的 underlying concept，使用 quantity 和 container 方法在这个类中也失去了意义。

所以，不要这么做。

### 6.7. Prying Open the Factory

前面说到，工厂方法还不是 open 的，如果我们想产生一个新的类型的对象，必须修改工厂方法。

让工厂方法 open 有一些手段 (策略模式?)

在这个例子中，我们可以使用 Ruby 的元编程来实现，BottleNumber0, BottleNumber1, BottleNumber6 这些类名有一定的规律。

    class BottleNumber
      def self.for(number)
        begin
          const_get("BottleNumber#{number}")
        rescue NameError
          BottleNumber
        end.new(nubmer)
      end
      ...

虽然现在这个工厂方法 open 了，添加新的类型不再需要修改这个方法，但也带来了一些缺点：

1. 代码更难懂了
1. BotterNumber0, BotterNumber1, BotterNumber6 在代码中不再明显了，导致的结果有可能是，程序员全局搜索 BotterNumber0，发现没有地方使用它，就把 BottleNumber0 的实现删掉了
1. 使用了不推荐的异常控制流程 (不推荐吗?)
1. 如果新的类型的名字不再遵循 "BottleNumber#{number}" 的规律，那么这个实现就还是需要再次修改。

所以，实现工厂方法到底有没有 Open 的必须，取决于实际情况。如果以后不会再增加新的类型或极少增加，那么工厂方法就没有 Open 的必要性。

### 6.8. Summary

略。

最后的代码是如此简洁优雅，这就是代码之美！
