---
title: 如何优雅的编程 - 来自《代码大全》
date: 2019-06-08
tags: 
- 代码大全
- 优雅编程
- 管理复杂度
- 编程
---

来自《代码大全》内容信息整理，豆瓣评分9.3。

> Write Programs for People Frist, Computers Second.
> 首先为人写程序，其次才是机器。

<!-- more -->
## 软件构建的世界 

### 什么是软件构建

软件开发中的各类不同活动：定义问题、需求分析、规划构建、软件架构、详细设计、编码与调试、单元测试、集成测试、集成、系统测试、保障维护、

软件构建一般也会被认为编码 coding，或者编程 programming。而构建感觉就不显得那么机械化，而需可观的创造力与判断力。构建活动主要集中于编码与调试，但也牵涉来除定义问题以外的所有活动。

![](http://image.freefe.cc/2019-06-28_08-41-55%20%281%29.png)

### 软件构建为何如此重要

- 构建活动是软件开发的**主要组成部份**。根据项目的不同，一般占据项目的 30% - 80% 的总开发时间。
- 构建活动是软件开发的**核心活动**。需求分析与架构设计都是项目的基础工作，使得我们更有效的构建。测试则为构建活动的后续工作，用以验证构建的正确性。
- 将主要精力集中于构建活动，可以大大**提高程序员的生产效率**。不同程序员的生产效率差异可达10-20倍（1968）。后续该结果被大量研究所验证。
- 构建活动产物-源代码-往往是**对软件的唯一精确描述**。很多项目，程序员唯一的到的文档就是源代码本书，因此，源代码就比必须有尽可能高的质量，利用各类技术来改善源代码从而得到一个详细、正确且信息丰富的程序。
- 构建活动是**唯一一项确保会完成的工作**。不管项目如何紧急，不管舍弃哪一环节，构建活动是不可或缺的环节，所以构建活动进行改进，是对改进软件开发过程一种有效的途径。构建活动的质量对于软件的质量有着实质性的影响。

## 隐喻，更充分的理解软件开发

计算机科学领域中有着丰富的语言，**病毒、木马、蠕虫（worm），臭虫（bug）、崩溃（crash）、错误**等。这些隐喻描述了软件领域中的各类特定的现象和事物，让我们更深刻的理解软件开发的过程。

### 隐喻的重要性

> 通过把你不太理解的东西和一些较为理解、十分类似的东西做比较，你便可以对不理解的的东西产生更深刻的理解，这类形式的隐喻则为『建模』。

化学家库勒梦见咬着自己尾巴的蛇，从而提出来环状分子结构，解释了苯环的各种特性。还有比如气体分子的运动理论、钟摆的思考、地心说，日心说等。隐喻都发挥着十分重要的力量。

在软件方面，隐喻好比是一个探照灯，不会告诉你去哪里寻找答案，而是告诉你如何寻找答案。隐喻的作用更像启示，启发式方法，而非算法。

![](http://image.freefe.cc/2019-06-28_09-19-35%20%281%29.png)

**算法是一套明确的指令**，来完成具体的任务。可预测、确定性、不易变化。从A到B，不会额外让你绕到C、D等地方。如驾车到某人家，算法是这样的：沿着X公路往南行进160公里到达Y，从Z口出往西南山上开12公里，杂货铺边红绿灯右转，就是M大街N号。

**启发式方法则是一种寻求答案的技术**，具有较强的偶然性，仅仅引导你如何寻求解决方法。比如上面例子，启发式是这样解决的：找出之前的信，照着地址来到这个小镇，然后问一下小镇的人们，基本都知道我们，并且应该很乐意帮助你，如果找不到人，直接打电话给我们，我们会出来接你。

算法与启发式方法存在一定的相似，区别有点类似解决问题的间接程度。如果有一些明确改如何解决编程问题的信息，编程当然会更容易，结果也更易预见。但编程还远远没那么先进，或许永远不会那么先进。目前编程最大的挑战依旧是将问题概念化，正因为每一个问题在概念上都是比较独特的，所以要寻找一套能解决所有问题的通用指导就不太现实。那么对于各类问题，能知道大致上如何一般性的解决也就非常有价值来。

隐喻将提高我们对于编程问题和编程过程的洞察力，帮助思考编程过程中的活动，想象出更好做事的方法。

### 软件的隐喻

![](http://image.freefe.cc/Snipaste_2019-06-28_08-45-08.png)

#### 写作代码

这个隐喻暗示着开发一个程序就好比写一封有缘由的一样，坐下来，拿出纸和笔，从头到尾的进行书写即可，无需做正规的计划，想到什么写出来便是。

对于个人规模的工作，或者小型项目来说，这类写作的模式已经足够了，然而对于其他场合而言，却远远不够 - 没有完整、充分的刻画软件开发工作。书写通常是一个人的活动，而一个软件项目多半会设计许多不同职责的人，写作或许你写完就完成了，但软件开发编码完毕后可能只是完成了一部分工作。对此更重要的是软件构建更注重于复用以往项目的设计思想、代码模块以及测试用例的开发效率，由于写作所要求的更注重原创。

故而以写作隐喻软件开发过程，则太过于单调与呆板。

#### 培植系统

一些软件开发人员认为，应当将软件开发想象成播种与耕作的形式，一次设计系统的一小部分，编写一小块代码，做一点测试，然后将其成果一点点的添加到系统中去。

而作者认为，该模式中的增量技术是很有价值的，但是将其比喻成播种，耕作就不为恰当。犹如你对系统计划施肥，对设计细节蔬果，通过有效的管理来增加代码产量。问题在于暗示我们无法对于软件开发的过程和方式进行直接控制，你需要一点一点春天播下种子，按照节气处理照料，将在秋天活动丰盛的代码。这显然也不恰当。

#### 养殖系统：系统生长

该隐喻类似我们如何一次为软件系统增加一个小部分。与生长相关类似词：增量的、迭代的、自适应的以及演进的。以增量方式进行设计、编译和测试，都是目前已知最强有力的软件开发概念。

在增量式开发时，预先做一个尽可能简单的、能运行的版本，不必有真实的输入，也无需对数据进行真正的处理以及真实的输出，仅仅需要构建足够强壮的骨架用以支撑未来的真实系统。对于你标注出的每一项基本功能，可能都仅需调用虚拟的类来进行占位。

骨架形成后，开始一点点的附着肌肉与皮肤，使用真是的类、接受真实的输入与输出、每次更新增加一小部分代码直到一个完整的工作系统。

作为隐喻，增量式开发的优势在于未做过度的承诺，对于耕作来说，将其作不恰当的引申更为困难一些。

#### 软件构建：建造软件

![](http://image.freefe.cc/2019-06-28_08-47-13.png)

建造软件这一说法则暗示了软件开发中的诸多阶段，如计划、准备执行等，更具所建造软件的种类不同，各类阶段和程度也可能变化。

如果你要搭一个易拉罐塔，你就需要一双稳健的手，一块平摊的地以及十几个完整的易拉罐就能简单的开干。而你要搭一座100倍的塔，那么100倍的易拉罐就远远不够，需要采用完全不同的计划方法和建造方法，并且不那么执着的话，还有建筑材料。

如果你想盖一个简单的建筑，比如一个狗窝，你要去木材点买一些木材和钉子，找一些使用的工作，或许傍晚时分，你的爱犬就有新窝了。可能狗窝忘了加一个门或者其他的怎么漏子，不过没关系，修改一下或者从头再来，顶多就是一个下午的事情。宽松的设计对于小型项目还算合适，如果你写了1000行代码，发现设计存在问题，你倒是也可以重构或者重新再来，不会损失太多。

如果你是在建造一栋房子，那么建造过程就好复杂的多的多，糟糕的设计也会带来严重的后果。

- 1、你要十分明确的准备建造一个什么类型的房子 - 软件开发中类似问题定义（problem definition）
- 2、你需要与建筑师探讨总体设计，并且确认。 - 软件的架构设计（architectural design）
- 3、画出你详细的蓝图，找一个承包人。 - 软件的详细设计
- 4、你要准备好建造地点，打好地基，搭建框架，砌好墙，盖好屋顶，通好水电煤气等。 - 软件的构建（construction）
- 5、庭院设计师、油漆匠、装修工来将新房子美化一番。 - 软件的优化（optimization）
- 6、整个过程会有各类检查者检测工地，地基，框架与布线等各类地方。 - 软件的评审（reviews）和详查（inspections）

以上两种活动，更高的复杂度与更大的规模都会带来更多的结论。建材与人力的消耗。推到一堵墙后移半米的代价是相当昂贵的。你只有把房屋详细的设计好，才能不浪费资源去修正本可以避免的麻烦。开发软件，或许原材料是廉价的，但是人力成本甚至是市场的机会成本代价高昂。

**两类活动还有其他什么相似之处呢？**

建造一个房子的时候，你不会去试着建造那些能买到的一些东西。你会去买洗衣机，烘干机，洗碗机，电冰箱，但你们应该不会去自己动手造这些东西。
开发软件也一样。不会自己去处理一些系统层次的代码，回去寻找一些开源的程序，现成的能力，比如一些科学计算函数，用户界面组件，数据库访问等。自己编写那些能买到的现成的代码，通常一般情况下是没有太大意义的。

但是，更高一层来说，如果你想建造一间拥有一流家具的高档住宅。那么你可能需要定制洗碗机，电冰箱等高档家电。就如软件开发中一样，如果你要开发一款一流的软件，你就可能需要要自己编写科学计算函数，以获取更高的速度和更高的精准，自己编写容器内用户界面组件以及数据库访问等，使得你的一流的产品的各个部分都无缝连接，拥有一致的外观和体验。

**软件构建 类比 建筑构建**

适当的多层次的规划，对于建造建筑物和构建软件，都是有较高的好处的，如果你按照错误的顺序构建软件，那么编码测试和调试都很困难，需要花更长的时间才能完成，甚至整个项目干脆就分奔离析，由于每个人的工作都过于复杂，所有成果组合在一起的时候就会变得混乱不堪。

精心计划，并不是说什么事都巨无细的计划或者过度的计划，类似你在考虑房屋结构的时候，主要考虑结构性支撑的规划就行，没必要即可去考虑你是用地板还是瓷砖铺地。软件开发也一样，规划是否得当，体现在项目在后期改变细节上能力扩展的便捷性。

用建筑房屋来类比，软件构建还有助于解释为什么不同软件项目能从不同开发模式中获益。你去盖一间仓库，或者工具间，是盖一个医院，甚至是核反应堆，你在规划设计及质量保证方面的需求程度都是不一样的，所用的方法也是不一样的。所以说，有时软件开发中通常你只需要用灵活的，轻量级的方法，但有时候你就必须用严格的，重量级的开发方法以达到安全性目标，或者其他什么目标。

最后，建筑这一隐喻，让我们对超大型的软件项目认识更加深刻。超大型的结构一旦出问题，后果是非常严重的，所以有必要对这样的结构进行超出常的规划和建设。建筑人员需要非常小心的定制并核查计划，规划在建筑时留有余地，以保障安全。宁可多花10%的成本买更坚固的材料，也比摩天大楼倒下来划算的多。在美国纽约帝国大厦建造的时候，每辆运料车的运输时间都有会留有15分钟的余地，但如果每辆车在运输的时候没有指定到位，那么整个工期都会延误。

按房屋建造这一隐喻，很多软件开发中的术语都是从建筑中衍生而来的。软件架构、支撑性测试代码、构建、基础类等。

对于隐喻来说，不同的隐喻彼此并不排除，但使用恰当的隐喻能带来更多的优势，比如评估问题处理方式，相关优先级的思考，代码的编写逻辑与质量等。


## 三思而后行：前期的准备

准备工作的中心目标是降低分险：一个好的项目规划者能够尽可能早的将主要的风险清除掉，使项目的大部分工作能够尽可能的平稳进行。

软件中最常见的项目风险是糟糕的需求分析和糟糕的项目计划，因此准备工作就倾向于集中改进需求分析和项目规划。

- 1、并不是所有的专业的程序员都知道准备工作的重要性，并且在进入构建活动之前会去检查所有的先决条件是否已满足
- 2、大部分分配做前期准备活动的开发人员并不具备完成这项任务的专业技能。项目规划，分析全面而准确的需求，创建高质量的架构活动等等，都需要较高的经验与技能。
- 3、有很大一部分程序员，知道如何进行前期工作，但是并没有去做，因为他们不能够抵抗尽快开始编程的欲望，以及从项目侧的一些排期压力。

比如在除了程序员侧各方看来，在项目开发阶段，他们只是关心100个功能每天实现多少，几天可以全部完成。这是从他们角度所关心的，并没有错。而程序员角度上，我们需要关心的则更多，以及如何更简单的或者朦胧的回答我们做的不仅仅是完成功能那么简单。

实现高质量的实践方法是那些能创建高质量代码的程序员的共性，很重要的一点是在项目的前期、中期以及后都强调质量。这也是《代码大全》要讲诉的整体主要内容。


### 问题定义的先决条件

首先要满足的一项条件是：对这个系统要解决的问题做出清楚的描述。这个更多偏向于PM对于产品的定位，而程序员也应当明确的知道我们的目标以及面对的问题。

### 需求的先决条件

明确需求的意义：
- 1、有助于用户，而非单单程序员驾驭系统的能力。也避免程序员在编程期间自行决定一些含糊不清的需求。
- 2、有助于避免争论。目前RD与PM之间的摩擦大部分是由于需求在一定角度上不明确产生的。
- 3、有助于开始编程开发后的系统变更情况。或许代码上的错误，你只需修改几行就能解决，但是如果是需求上的错误，那就有可能需要整体改变设计与架构。

众多组织数据显示：大型项目中，在架构阶段检测到需求错误，其修复成本是：需求阶段检测并修复的成本的3倍。编码阶段检测到需求错误，成本是5-10倍。系统测试阶段是10倍。发布阶段是10到100倍。

所有详尽的需求描述，是项目的关键。甚至一些情况可能比有效的架构技术更重要。

### 需求稳定的神话

需求稳定是软件开发的圣杯。项目就能有序的、可预测的、平稳的进行，完成架构到设计到编码到测试等一系列工作。早期IBM与其他公司数据显示，开发过程中平均会有25%的需求变更，因变更导致的返工预占比75%以上。这主要是1980年到2000的相关，目前的程序员或许相对来说对于变更更加适应一些或者做好来一些预设的准备，但数据上估计没有太好的改善。

我们如何预备应对需求变更？

- 1、使用需求质量核对表来评估你的需求质量：开车开错的地点，下车检查路线肯定不算是浪费时间。
- 2、确保相关方明确了解需求变更的代价：提及进度与成本，往往对各方有效，许多必须有可能就变成来最好有。
- 3、建立变更的控制机制
- 4、使用能适用变更的开发模式：比如敏捷开发，缩短开发周期，更快速的需求迭代等。
- 5、放弃项目
- 6、注意项目商业案例

**需求核对表**

- 功能需求：明确功能的各项细节，包括输入输出，软硬件借口与通讯协议，用户期望功能，以及各项任务的目的等。
- 非功能需求：用户的操作，体验，安全性等问题考量。系统的可靠性、可维护性，可扩展性等考量。
- 需求质量：需求间的一致性，清晰详细程度，健壮性与正确性等。测试变更状态等调整的应对。
- 需求完备性：对于需求产出前的前置依赖，需求不完善或者变更后的可接受程度等。

### 架构的先决条件

软件架构是软件设计的高层部份，用于支持软件更细节的一些设计与开发工作。离开了良好的架构，可能瞄准来正确的问题方向，但却使用来错误的解决方案，导致来不成功的构建。

#### 架构的细节组成部份

- 1、程序组织：首先要以概括的形式将有关系的系统做出分解和概述。定义程序的主要的构造块，更具程序规模不同，每一个构造块可以是单个类也可以是子系统等，各自实现一种高层功能，相互协调产生了系统。
- 2、主要类：应当较为详细的定义主要类。80/20法则，对于构成80%行为的20%的类进行详细说明。
- 3、数据设计：应当描述主要文件与数据表的设计。包括设计某种模式（数据结构与算法）的原因与考虑点等，以方便后期的维护。
- 4、业务规则：关于业务内特定的业务逻辑，以及对于系统的设计影响。
- 5、用户界面设计
- 6、资源管理：对于稀缺资源的管理计划，包括数据库链接、线程、内存等整体的规划。
- 7、安全性：设计实现设计层面与代码层面的安全性问题。比如处理缓冲区方法，处理未经处理数据（用户输入、cookie、配置数据等）的规则，加密，错误处理程度等事宜。
- 8、性能：评估数据量，复杂程度以确定系统达到性能目标。
- 9、可伸缩性：系统增长、扩展在系统各方面满足未来的能力。
- 10、互用性：系统与其他软硬件的共享数据或者资源。
- 11、国际化/本地化
- 12、输入与输出：定义数据的读取策略，以及错误检测的处理位置。
- 13、错误处理：由于错误处理牵扯整个系统，最好在架构层次上对待明确。
- 14、容错性：定义所期望的所有容错类型，作为增强系统可靠性的方式，包括错误检测以及错误回复等。
- 15、架构的可行性：关注系统的各项能力是否符合标准，比如性能目标、资源与环境支持度等。
- 16、过度工程：健壮性表示系统检测到错误后继续容错运行的能力。架构时需要明确，是应该做出最简单的能工作的东西还是为了机身起见宁可过度工程。避免系统中某些类异常健壮，而其他类勉强健壮。系统的强度不是取决于最薄弱的环节，而是所有薄弱环节的乘积。
- 17、关于买还是造的决策：最激进的构建解决方案就是更不不去构建，直接购买或者使用开源软件。其取决的条件在于我们自己定制的组件，应该在某些方面胜过现成的程序库和组件，并且收益与成本是正向的。
- 18、复用决策：如何复用软件的加工使之符合其他架构的目标
- 19、变更策略：应列出有可能的增强性的功能，并且考虑其扩展接入的方式。
- 20、架构的总体质量：
优秀的架构文档特点在于，讨论系统中的类，每个类背后的隐藏信息，各类方案采纳或者被替换的根本理由等。架构应该是带有少许特别附加物的精炼且完整的概念体系。是《人月神话》的中心主题。
优秀的软件架构和机器与语言无关，应当明确指出风险并且解释，多个视角看待项目。不应当包含任何对你而言难以理解的东西。


# 创建高质量的代码

## 软件构建中的设计
许多软件构建的设计就是程序员在键盘前完成的，或许用伪类写出一个类的接口，或许编码前画一下几个类的关系图，也可能咨询你下更有经验的程序员。无论何种方式进行，如果能认识到设计在程序员侧也是一项明确的活动，那么我们将受益匪浅。

软件的设计目标是组织良好，干净利落。然而形成的过程却可能了无章法。或许设计时就会犯很多错，但是这正是关键所在，设计阶段的犯错并且加以改正，其代价远远小于编码后发现错误改正的成本。这正是我们所期望的。

软件构建的设计就是确定取舍，调整顺序的过程，并且收到较多的限制，在各类限制条件中寻求方案的过程。是类似启发式，不确定的一个过程，然后自然而然形成的。

## 关键的设计概念

软件的首要技术使命：管理复杂度

《没有银弹：软件工程中本质性与偶然性》Fred Brooks（《人月神话》的作者）。

**偶然的难题与本质的难题**

Fred Brooks 引用了一个哲学的概念来说明：本质属性是一件具体事物必然具备的属性。偶然属性则表示一件事物恰好具备来某些属性。

类似与软件开发，其中大部分偶然性难题在很多已经被解决。比如一些与笨拙语法相关的偶然性难题，基本随着语言的迭代与发展被清晰的解决处理，那么显然这些问题的重要性也会逐渐下降，比如早期低端操作系统的各类问题。但是剩下的本质性问题，相对解决的比较缓慢。

本质上来说，软件开发意味着不断的去发掘复杂的、相互链接的一套系统的所有细节。本质性的困难来自多方面：面对复杂和无序的现实世界、精确并且完整的识别依赖与特例、设计准确性高的解决方案而非一般方案、并且人类对于认识现实世界本身都还有很大困难，那么基于此的软件开发来解决大规模的现实问题，难度可想而知。

但是，不论是偶然性问题还是本质性问题，其触发的根源都在与 复杂度。

**管理复杂度的重要性**

在做项目失败的原因确认时，很少会将技术原因作为项目失败的主要原因。或许是不尽人意的需求、规划或者管理。但是如果确定是技术原因导致的失败，那么一定是失控的复杂度。软件变得极端复杂，开发者无从知晓模块的功能、逻辑等，完全无法维护的状态，那么项目就估计快停了。

所以说，在作者看来：软件的首要技术使命便是管理复杂度。

类似我们的大脑，软件开发人员不应该尝试在同一时间将整个持续程序塞进去，而是应该试着以某种合理的方式去组织程序，以便在任意一个时刻，我们都是专注于某一个特定的部分，减少时间点上所需要考虑的程序量级。

例如：将系统分解为多个子系统，各项子系统之间减少互相依赖。保持子系统的短小精悍也有助于减少思考的负担。从问题入手而不是直接从底层去实现细节编程，在最抽象的层次工作。

**高代价、低效率问题**

- 1、复杂的方法解决简单的问题
- 2、简单但是错误的方法解决复杂问题
- 3、不恰当的方法解决复杂问题

类似编写一个基础函数，开发者应该首先明确这是基于固定场景的，还是基于当前系统基础的，还是准备开源做到较多形式的支持的。都会影响我们对于当前函数、参数、细节实现的复杂度确定。

那么，将同一时间需要处理的本质复杂度降低到最小，并且不要让偶然性的复杂度无所谓的快速增长是一个不错的方式。

- 1、最小的复杂度。设计的主要目标是降低复杂度，避免做出自认为聪明的设计，往往聪明的设计会难以理解。
- 2、易于维护。开发过程中，应当不断考虑逻辑、细节对于后期维护人员是否会产生负担，更多的产出可自说明的系统。
- 3、松散耦合。在设计时，让各个组成部份关联最小。通过合理的抽象、封装、隐藏信息等进行优化，减少关联依赖，并且对于强关联的逻辑业务能合理的设计与处理。
- 4、可扩展性。增强系统能力而无需破坏基础结构，通过改动某一部分而不影响系统其他部份。
- 5、可重用性。系统的组成部份能在其他系统中复用。
- 6、高扇入与低扇出。让大量的类使用某个给定的类，意味着很好的利用了较低层次上的工具类。一个类内少量或者适中的使用其他类减小过分依赖与复杂。
- 7、可移植性。相对于系统能较方便的迁移至其他环节。
- 8、精简性。系统没有多余的部份来添加成本。
- 9、层次性。尽量保持系统各个分解层的层次性，使我们可以在各层次上观测系统。
- 10、标准技术。制定系统的标准化、常用的方法等。如基础技术栈，基础函数库等。

## 设计构造块：启发式方法

**找出现实世界的对象**

辨识对象，以及其属性。确定对象可进行的操作以及可对其他对象进行的操作。对象各部分的公有、私有属性。开放的公共接口。

**形成一致的抽象**

抽象是让你在关注某一概念的同时可以放心的忽略其中一些细节的能力。类似与可以在不同层次处理不同的细节。如我们把一类东西成为房屋，而不是玻璃、木材、钢筋等组合体，就相当于是在抽象的表现来。

基类也是抽象，集中注意力关注一组派生类的共同特性，在此基础上去忽略派生类的其他的一些细节。很大程度上，抽象能让我们在复杂的状态下专注于某些点，忽略无关的细节，降低思考上的复杂度。

**封装实现细节**

封装是对于抽象的补充，很多抽象不关注的细节，我们就可以使用封装来进行处理，隐藏重要性不高的一些复杂度上的细节。

**继承简化设计**

继承的好处在于可以很好的支持抽象的概念，继承简化编程工作，你可以写一些子程序拥有基本属性的项，然后另一些处理依赖基本类的特定的类。

**信息隐藏**

信息隐藏是结构化程序和面向对象设计的基础之一。也是首要技术使命中重要的启发式方法，其强调的就是隐藏复杂度。

设计一个类，一项关键性的决策就是确定类那些特性应该对外可见，哪些特性应该隐藏。好的类接口应该犹如冰山，让类的大部分内容不会暴露。

1、隐藏复杂度，我们一般情况就无需关注细节的复杂度
2、隐藏变化源，变化影响限制在固定范围内。

**找出容易改变的区域**

好的程序设计面临的重要的挑战就是适用变化，目标应该将不稳定区域隔离，将变化带来的影响限制在一个子程序或者一个类的内部。使得系统内你能考虑到的变化的可能性与其影响范围成反比。

**保持松散耦合**

耦合度表示类或者子程序之间关系的紧密程度。其设计目标是创建出小的，直接的，清晰的类或者子程序，使之间的关系尽量灵活，即松散耦合。

耦合的种类：

- 1、简单数据参数耦合：两个模块通过参数来传递数据，并且参数均为简单数据类型。耦合关系正常，可接受。
- 2、简单对象耦合：为模块实例化出的一个对象，两者的关系。该耦合关系也是正常的。
- 3、对象参数耦合：传递数据的参数为对象，那么调用方需要了解参数对象的细节，那么相对来说耦合关系会紧密一些。
- 4、语义上的耦合：最复杂的耦合关系，两者间不仅使用来语法上的逻辑，还是用了模块内部工作细节。

如：
- 1、M1向M2传递一个控制标识，告诉M2来做什么。那么就需要M1对M2内部工作细节有所了解，对于控制标识是如何使用的。
- 2、M2在M1修改来全局变量或者闭包数据后使用数据。这就需要M2对M1数据的修改都符合M2的需求。
- 3、M1要求在调用M1.B()时必须先进行初始化 M1.A()，而M2知道内部逻辑，在调用M1.B() 时必然会调用初始化，所有直接调用M1.B()。

**设计模式**

常见的设计模式：适配器、桥接、装饰器、工厂方法、观察者、单例、策略等。

设计模式通过现成的抽象来减少复杂度。将常见的解决方案细节予以制度化来减小出错。并且通过多种设计方案带来启发性的价值。以及将设计对话提升到更高层次上来简化交流。

设计模式潜在的陷阱是：强迫代码适用某个模式，如果对于代码做微笑的调整来符合某种通用的模式，可以使得代码梗易于理解，但是如果做巨大改动，强迫去符合某个标准模式，反而会增加复杂度。

设计模式是一种强大的管理复杂度的工具，但是不要为了模式而模式。

**其他启发式方法**

高内聚性、构造分层结构、严格描述类说明、分配指责、画一个图、保持设计的模块化等。


## 可以工作的类

### 抽象数据类型

抽象数据类型指一些数据以及这些数据所进行的操作的集合。把其定义为有一组操作的数学模型太过空洞。将链表中添加一个节点，你可以看做表中插入单元格，火车模型添加车厢等形象问题。

比如编写的程序内，可以使用不同的系统字体、字号以及文字属性的功能，如果我们将其作为一个抽象数据类型，那么我们就可以将设置的数据值以及相关的设置操作作为一个抽象数据类型进行考虑可管理。（可以看做如何抽象一个子程序的逻辑）

一般业务逻辑：

```
currentFont.size = 16;
currentFont.bold = true;
```

抽象数据类型: 子程序代码或许很短，但是你将字体的各类操作进行了独立与隔离，这样就为其他部分程序提供了更好的抽象。对于字体操作提供一层保护。

```
currentFont.setSize(16);
currentFont.setBoldOn();
currentFont.setBoldOff();
```

- 1、隐藏实现细节
- 2、该抽象上的改动不影响整个持续
- 3、关注点更聚焦，提供相关联的依赖接口
- 4、更易提高性能
- 5、程序的正确性更显而易见
- 6、程序更具有自我说明性
- 7、数据传递与依赖更小化
- 8、操作实体而无需关心底层实现上的操作


### 良好的类接口

创建高质量的类，第一步就行创建一个好的接口，通过接口合理的展现和隐藏抽象。主要的评估方法为类所具有的公共子程序所构成的集合，即类的公共接口的合理性。

- 1、类的接口的抽象层次应该是一致的。比如对于雇员列表的雇员添加与删除，获取指定的雇员信息等，对于雇员类来说设计获取其相关信息而非同一层上的抽象。
- 2、需要明确我们抽象的是什么，需要处理的问题是什么，才能较好的梳理出合理的接口。
- 3、提供成对的服务，比如字体开启与关闭加粗模式等。
- 4、接口可编程而非表达语义。每个接口都应该是可编程部份+语义部份组成，可编程部份可做相关的检测判定。
- 5、注意与接口抽象不一致的公共成员。
- 6、抽象性和内聚性考虑。


**良好的封装**

封装是一个比抽象更强的概念，如果没有封装，抽象很容易就被打破，无法较好的实现。

- 1、尽可能限制类和成员的可访问性
- 2、不要公开成员数据
- 3、避免私有的实现细节放在接口中
- 4、不要对于使用类的开发者做任何假设
- 5、让阅读代码比编写代码更容易
- 6、注意语义上的封装性的破坏
- 7、注意耦合关系



### 设计与实现的问题

**继承**

继承表示一个类是另一个类的一种特化。其目的在于『定义能为两个类或者更多派生类提供共有元素的基类』，来精简代码。

- 1、继承增加程序复杂度，所有必须详细说明或者不使用。
- 2、确保我们只是继承需要的部分。
- 3、将公共的接口、数据以及操作放到较高位置。
- 4、只有一个实例的类和只有一个派生类的基类需要重新评估创建的类必要性。
- 5、避免继承体系过深，以及多态导致大量的类型检查。

**继承的规则**
- 1、多个类共享数据而非行为，应该创建公共对象。
- 2、多个类共享行为而非数据或者共享数据以及行为时，应该让类从共同的基类继承而来，基类定义公用的子程序以及数据。

**构造函数**

- 1、如果可以，在构造函数中初始化所有数据成员。
- 2、优先使用深拷贝，除非论证可行才使用浅拷贝。

#### 创建类的原因

- 1、为现实世界中的对象建模。为现实世界每一个对象类型建模是一个重要的理由。
- 2、为抽象对象建模。比如经典的 Shape（形状）对象，及其派生的 Circle、Square等。
- 3、降低复杂度。降低软件首要使命的难度，创建类后隐藏信息和细节，使我们无需再去考虑。
- 4、隔离复杂度。隔离复杂的算法、大型数据集、复杂的各类协议等容易引发错误的点，分类集中管理。
- 5、隐藏实现细节。对于软件，我们不可能存入整个细节图，而是分块，分层的处理。
- 6、限制变动的影响范围。
- 7、隐藏全局数据。
- 8、参数传递顺畅。
- 9、建立中心控制点。
- 10、代码易于重用。
- 11、相关操作分类。


## 高质量的子程序

```
// C++

void Handlestuff(CORP_DATA & inputrec, int crntqtr, EMP_DATA emprec, double & estimrevenue, double ytdrevenue, int screenX, int screenY, COLOR_TYPE & newcolor, COLOR_TYPE & prevcolor, StatusType & status,
int expensetype)
{
int i;
for(i＝0; i＜100; i++) {
    inputrec.revenue[i] = 0;
    inputrec.expense[i] = corpexpense[ crntqtr ][ i ];
    }
    
UpdateCorpDatabase( emprec );
estimrevenu =  ytdrevenue * 4.0 / (double) crntqtr;
newcolor= prevcolor;
status = SUCCESS;
if (expensetype == 1) {
    for(i＝0;i＜12;i++) {
        profit[i] = revenue[i] -expense.type1[i];
    }
} 
else if (expensetype == 2 ){
    profit[i] = revenue[i] - expense.type2[i];
}
else if (expensetype == 3){
    profit[i] = revenue[i] - expense.type3[i];
}

```

这段代码有什么样的问题，有一些很明显的问题，还有一些我们没注意，自己也经常犯的问题。

1、命名较差，完全不清楚子程序是干什么的
2、没有文档说明
3、缩进布局很差
4、输入变量 inputrec 被重写
5、读写全局变量 profit
6、子程序没有单一的目的，初始化重写输入，更新数据库，计算，重写全局变量，完全无相关联系的操作。
7、缺少防御性 crntqtr = 0 时的问题
8、各类神秘数值 100、4.0、12、1、2、3等
9、未使用的参数 screenX、screenY
10、参数过多，建议上线为7
11、参数顺序混乱无注释

创建一个子程序有很多合理的原因，但是完成它的方式却分对错。

### 创建子程序的理由

- 1、降低复杂度，因为我们软件工程的首要技术使命就是管理复杂度。
- 2、引入中间、易懂的抽象，比如哗啦啦写了一堆逻辑处理代码，或者将其放到子程序中，命名：GetLeafName。
- 3、避免代码重复，这是创建子程序的最普遍的原因。
- 4、支持子类化，覆盖简短而规整的子程序所需要的代码比冗长邋遢的代码要少，并且减少犯错。
- 5、隐藏顺序，将细节执行顺序集中隐藏，而非到处散布。
- 6、隐藏指针操作。
- 7、提高可移植性。子程序隔离不可移植部份明确移植的兼容开发。
- 8、简化复杂的布尔判断。为理解程序的流程，通常没有必要去研究那些复杂的布尔判定的细节，将其放到子函数中提高整体可读性。
- 9、改善性能。子程序可以是我们集中一处单一功能点进行优化，并且使用更高效的算法和更快的语法也更容易调整。

编写子程序较大的心理障碍是：不情愿为一个简单目的而写一个简单的子程序，看起来大材小用，并且比较低端。


```
// 一个简单的 deviceUnits 到 points 单位转换
points = deviceUnits * ( POINTS_PER_INCH / DeviceUnitsPerInch() )

// 封装子程序后
function deviceUnitsToPoints(deviceUnits) {
    return deviceUnits * ( POINTS_PER_INCH / DeviceUnitsPerInch() );
}
points = deviceUnitsToPoints(deviceUnits);
```
封装子程序后，那么很多使用改转化的地方都可以统一使用子程序，进行集中管理。应对以后转化会变得更复杂的情况（比如对于 DeviceUnitsPerInch 的调用判定等 ）。并且子程序的命名是我们可以忽略执行细节让代码具有自我注解的能力。

### 子程序的设计

对于子程序而言，内聚性指子程序中各种操作之间联系的紧密程度，目标是让每一个子程序只把一件事情做好。

功能的内聚性：是最强最好的一种内聚性，让一个子程序只执行一项操作。我们可以观察到很多系统底层或者语言底层的基础子程序基本都是功能内聚的。

不够理想的：

顺序上的内聚性：指子程序内包含需要按顺序执行操作多个功能，按步骤共享数据的。建议按功能拆分解耦。

通讯上的内聚性：仅仅是子程序内不同操作使用的想同的数据，但操作不存在任何联系的。

临时的内聚性：一些因为需要同时执行才放到一起的子程序。比如我们在编写完一个大而冗余的代码时，加个init初始化函数，然后一股脑的吧开始需要执行的代码扔进来。临时性的子程序应当看做一系列时间的组织者，init可以包含读取配置文件，初始化数据等操作的管理者。应当明确内部的执行意义。

不可取的：过程上的内聚性，逻辑上的内聚性，巧合的内聚性。

### 好的子程序名字

1、描述子程序所做的所有事情。ComputeReportTotals 还不算完整，ComputeReportTotalsAndOpenOutputFile 算是完整，但是太长。如果子程序含有副作用，那么就会产生又笨又长的名字

2、避免无意义、模糊或者表达不清的动词。HandleCalculation, OutputUser, DealWithOutput 这类的是完全无法说明程序做了什么，达不到自我注解的程度。比如将 DealWithOutput 改为 FormatAndPrintOutput 就明了很多。

3、不要通过数字来形成不同的子程序。

4、控制子程序名字的长度，变量名一般建议9-15字符，好的子程序名字可以更长一些，视是否清晰易懂而定。

5、对返回值有所描述。cos(), userID.next(), printer.isReady() 等都是不错的例子。

6、给过程起名格式建议动词+宾语的形式。过程应当反应所做的事情。如printDocument(), checkOrderInfo()等，面向对象中则为 document.print()。

7、使用对账词。add/remove, insert/delete, open/close, show/hide, create/distroy 等。

8、为常规操作确定规则。统一 employee.id(), employee.get(), employee.getId()等不一致。


### 子程序长度

如果一个子程序大于200行，我们可能需要注意，是否会影响我们的维护成本。

### 子程序参数

比如使用所有参数，状态或者出错变量放置最后，不要把参数作为工作变量，数量限制在7个以内等。


## 防御式编程

主要思想是：子程序不应该因传入错误数据而被破坏，哪怕是其他子程序产生的错误数据。健壮模式应该检查所有外部的数据源以及子程序的参数，并且确定如何处理错误的输入。

### 错误处理技术

- 1、返回中立值。处理错误最佳的方式是继续执行操作，简单返回一个无危害的数值。
- 2、换用下一个正确的数据。比如处理数据流时，继续读取知道正确的数据。
- 3、警告信息记录日志。
- 4、返回错误码。指定统一的错误码表进行处理。
- 5、调用错误处理子程序或者对象。统一标准化处理错误情况。
- 6、显示出错信息。
- 7、局部处理错误。这个模式给予程序员很大的灵活度但是也带来很大分险。
- 8、关闭程序。

正确性意味着永远不返回不准确的结果，哪怕不返回结果。健壮性意味着不断的尝试采取措施保障软件继续运行下去，哪怕一些结果不准确。人身安全攸关的更倾向于正确性而非健壮性，比如放射性治疗仪。消费类软件则注重健壮性而非正确性，我们并不期望因为数据的错误直接关闭软件的形式。

### 防御式编程的姿态

防御式编程存在一种矛盾，开发阶段希望错误引人注意，而发布阶段尽可能的偃旗息鼓。

保留那些检查重要错误的代码，去除细节错误检查的代码，去除导致崩溃的代码，记录错误信息。

过度防御式编程也会引发问题，如果每一个地方使用所有的方法检测参数数据，那持续将变得臃肿而缓慢以及无止境的提高了程序的复杂度。所有考虑好那些地方需要进行防御以及因地制宜的调整防御式编程的优先级。

## 伪代码编码过程

![image](http://image.freefe.cc/Snipaste_2019-06-09_13-48-33.png)

创建类的总体设计：定义类的职责，所有需要隐藏的信息，精确的定义类的接口等各类信息。
创建类中的子程序：创建需要的子程序，在过程中可能还会引出各类重要的不重要的子程序等。
复审并测试整个类：对类进行整体测试以发现在该类层面的问题。

子程序的实现基本都是简单、直接了当的。

伪代码指某种用来描述算法、子程序、类或者完整程序的工作逻辑，非标准的记法。伪代码编程过程通过书写伪代码而更高效的创建程序代码的方法。

1、用英语或者其他语言来精确描述特定操作。
2、避免使用特定的语言中的语法，在一个比代码层次略高的层面上设计。
3、在基于真正的目的层面上编写伪代码

伪代码逻辑完毕后，我们应该可以基于此来实现生成代码，同时可以将伪代码转化为注释，更好的解释说明代码的用意。

1、这类模式的伪代码使我们项目、代码评审变得更为容易。
2、伪代码支持反复的迭代精华思想。
3、伪代码是我们变更更加容易。
4、使代码的注释工作变得更少。
5、比其他形式的设计文档更易于维护。

### 编写子程序

![image](http://image.freefe.cc/2019-06-09_14-25-38.png)

首先写出子程序接口声明。将首尾语句填充（函数申明，大括号区域等）并且将伪代码变为注释。虽然还没有任何代码，但是通过注释能理解整体的工作流程。而后填充代码以及编码工作。开发过程中，期间不断以及验证工作，最后进行收尾工作。


## 变量

利用构建活动来填补需求以及架构中存在的细小间隙问题是一种行之有效的做法。但是将蓝图设计精细到完全展现出细节则是一种低效的做法。构建活动中基础的组成就是变量的使用。变量同时指代对象和内置数据类型。

**隐式声明**

隐式变量声明对于任何一种语言都是最具危险的特性。关闭隐式声明，声明全部变量是一种避免问题的很好的做法。

**变量初始化原则**

不可理初始化数据是产生编程问题常见根源之一。

1、在声明时初始化，作为常用的防御式编程便捷有效的方法之一。
2、理想情况，在靠近变量第一次使用的位置申明以及初始化。集中申明的方式更容易出错，易于集中管理却并不易于维护。
3、尽可能使用final或者const。在一些明确的情况下，明确申明类型能减少维护的复杂度。
4、注意计数器与累加器的变量。
5、类的构造函数内初始化数据成员。易于管理降低复杂度以及方便底层的内存管理等。
6、设置编译器自动初始化以及其警告信息。

**作用域**

作用域或者可见性指的是变量在程序内的可见与可引用范围。各类语言处理方式肯不太一样。

介于同一变量多个引用点之间的代码，可称为攻击窗口。窗口内的操作就有可能引发变量北重置等问题。

把变量的引用点集中起来能较好的提高程序的可读性。
```
// e.g. 1
a = 0;
b = 0;
c = 0;
a = b + c; // a跨度2 b跨度1 c跨度0

// e.g. 2
a = 0;
b = 0;
c = 0;
b = a + 1;
b = b / c; // b平均跨度 0.5

```

![image](http://image.freefe.cc/2019-06-09_16-14-04.png)

与跨度类似，保持较低的存活时间也是我们的目标，原因与最求较小跨度一致，保持短的存活时间主要也是缩短攻击窗口。降低修改代码对其的影响降低。

**减小作用域的原则：**

- 1、循环开始时初始化循环使用的变量。
- 2、变量使用之前再申明赋值。
- 3、将相关语句集中在一起。
- 4、将相关语句组提取成单独的子程序。
- 5、开始使用最严格的可见性，然后更具需要扩展变量作用域。

有的程序员将很多变量放置到全局中，因为全局变量使用最为方便，无需考虑作用域相关的规则。其他程序员尽可能的使用变量局部化，有助于提高智力上的可管理性。『方便性』和『智力可管理性』两种理念归根结底是侧重写程序还是读程序。当然我们需要侧重的点是显而易见的。


### 变量名的力量

```
// e.g. 1
x = x - xxx;
xxx = fido + salesTax( fido );
x = x + lateFee( x1, x ) + xxx;
x = x + interest( x1, x );

// e.g. 2
balance = balance - lastPayment;
monthlyTotal = newPurchases + salesTax( newPurchases );
balance = balance + lateFee( customerID,  balance) + monthlyTotal;
balance = balance + lateFee( customerID,  balance);
```

很显而易见的一个好的变量名对于可读性、易记性是多么重要。当然变量名需要完全、准确的描述变量所代表的事物。

对于日期，currentDate 和 todaysDate都是较好的名称，直白准确的描述。而current，date其实稍微推敲则并没有明确当前，和日期到底是想描述什么。

一个好的变量名应该是从『what』出发，而不是『how』。一条员工数据可以称作inputRec或者employeeData，inputRec表示一个输入，记录的的计算机概念术语，而employeeData直指问题领域，与计算机无关。

**特定类型的数据命名**

**循环下标命名：**
i，j，k已经是约定俗成的。如果需要在循环外或者是较复杂的循环，那么就需要使用一个有意义的命名。

**状态变量命名：**
去一个比flag更好的名字。如果你发现你需要去猜测某段代码时，就该考虑变量的重新命名。猜测谋杀案谁是凶手是ok的，但是猜测代码应该是完全没有必要。

**临时变量命名：**
临时变量也不光应该只是 temp，或者x等模糊缺乏描述性的名字。

**布尔变量命名：**
使用有用的标识，done、error、found、success或者ok等明确的布尔标识。并且尽量使用肯定布尔名，反例 notFound，notDone。
尽量赋予真假的含义，部份喜欢添加is，isdone、isFound、isComplete等，优点在于可以将模糊不清的名词，isStatus，缺点在于降低了逻辑表达式的可读，if(isFound) 与 if(found)。


**非正式的命名规则**

- 1、区分变量名与子程序名字
- 2、区分类与对象
- 3、标识全局变量
- 4、表示成员变量


按实际变量名命名含有三类信息：变量内容是什么，数据的种类以及变量的作用域。【标准化的前缀，通用的缩写】


### 基本数据类型

基础数据类型是构建其他所有数据类型的构造块。

**整数**
注意整数除法。7 / 10 = 0（JS中数值均为浮点型）。注意整数的溢出。注意中间值的溢出。

**浮点数**
主要问题在于十进制小数不能够精确的用数字计算机中的1与0来表示。避免量级差较大数之间的加减运算，避免等量判断等问题。

**字符和字符串**
注意一些特殊字符，了解你的语言是如何支持Unicode，国际化/本地化策略的考虑。

**布尔变量**
使用布尔变量对程序加以文档说明，简化判定逻辑代码。

**数组**
数组需要注意下标边界，多维数组的下标使用顺序等

### 不常见的数据类型

**结构体**

- 1、用结构体明确数据关系
```
// 容易误解的，无组织的
name = inputName
address = inputAddress
...

// 对象结构体
employee.name = inputName
employee.address = inputAddress
...

```

- 2、简化数据块的操作：传递一批相关数据 or 传递一个包含所有数据的结构体 
- 3、简化参数列表
- 4、减少维护：后期删除某一字段，如果是结构体内的，相对就会比较便捷

**指针**

概念上看，指针包含：内存的某处位置和解释改位置中的内容。


**全局数据**
使用全集数据的风险远大于使用局部数据的情况。

- 1、不经意间修改了全局数据
- 2、全局数据的使用阻碍代码复用
- 3、全局数据的使用导致模块代码非确定性的初始化
- 4、破坏了模块化和智力上的可管理性：其将原本的模块化，集中精力管理一点的模式牵扯到来其他模块。

避免使用全局变量，不只是因为它很危险，更由于我们可以使用更好的方法来取代。比如访问器子程序。


## 语句

### 直线型代码
从数据为中心 转到语句为中心的观点上来。直线型代码的原则就是：按照依赖关系进行排列组织代码，使用好的子程序名、参数列表、注释、变量等，将顺序的关系变得明显，并且利用注释 换行等，将我们的直线型代码更可读、分块更明显。


### 条件语句

**if语句**

首先书写正常代码路径，再处理不常见的情况。确保等量的边界判定正确。将正常的情况放在if后面，而非else。并且if内逻辑应当有意义。较多的if else判定建议使用switch等其他结构。

**case语句**

应使用最有效的排序，简化每个case的操作量。default应该只是检查真正的默认情况或者错误情况。


### 控制循环

循环用来指代任意一种迭代控制结构，使用循环是编程中最复杂的方面之一。也是创建高质量软件的一个决定性因素。

**循环的种类**

-** 计数循环**：逻辑代码执行次数一定，比如发放每位职员的薪资。
- **连续求值循环**：不确定执行次数，每次执行时检测是否执行完毕。使用场景：一些特殊的条件判定循环，实现不知晓执行的次数，状态由动态变量控制。检测位于头部或者尾部。
- **无限循环**：启动后无限循环。比如心脏起搏器等
- **迭代器循环**：对于容器内的每一元素执行，比如一些标准或者类库支持点forEach等

主要为灵活度的差异与检测是否执行完毕的位置有所差异。两者也决定了使用哪种循环。for循环使用于简单的用途，你在循环头中写完便可以忘却的不需要再关心的可是使用for，而如果存在一个必需是执行从循环中跳出的那边建设使用while。

**循环控制**

引发错误的情况：

- 1、忽略或者错误的对循环执行初始化
- 2、忽略了对累加变量或者其他循环有关的变量执行初始化
- 3、不准确的嵌套和循环中止
- 4、错误的增加了循环有关的变量值
- 5、不准确的循环下标访问

两种减少循环问题点方式：

- 1、减少影响循环的因素数量，简化、简化、再简化。
- 2、将循环内部作为子程序。

**循环三部分**

- 进入循环：一个位置进入，初始化位置，for循环or while循环、
- 循环体：{}隔离，避免空循环，循环内务操作位置，操作目标点唯一性、
- 退出循环：确认循环停止并且条件明显，退出使用break和continue小心谨慎

**循环的长度**
循环应当尽可能短，若接受编写简单代码原则，应该很少产出大于15-20行的循环。
嵌套限制在3层，若过长应当提取子程序或者简化控制结构。
子程序的应用。


## 不常见的控制结构

**子程序多出返回**
半途退出子程序的方式

1、若是增强可读性，则使用
2、防御性代码简化复杂错误处理
3、建议减少return数量

**递归**
一个子程序自己负责解决某个问题点一部分，并且将问题分成小块，调用自己来解决每一小块。

```
// 递归的快排
function QuickSort(array, firstIndex, lastIndex) {
    if (firstIndex < lastIndex) {
        const pointer = division(array, firstIndex, lastIndex);
        QuickSort(array, firstIndex, pointer -1);
        QuickSort(array, pointer + 1, lastIndex);
    }
}
```

递归将自身分解为两部分，计算本次的操作，以及切分更小的操作让自身函数继续执行。
小范围的问题，递归能带来简单优雅的解决方案，但范围扩大后带来的复杂度甚至是栈溢出等问题也会比较普遍。通常简单的迭代会更易理解。

1、确认递归能停止
2、递归限制在一个子程序
3、注意栈空间
4、避免递归阶乘或者斐波那契数列：速度缓慢，无法预测内存使用，更高的复杂度。


**goto**



### 一般控制问题

**布尔表达式**
除了最简单的顺序执行控制外，所有控制都依赖布尔表达式。

1、使用true和false进行布尔判定，而非0，1等。reportSelected===1，1是无法明确到底是什么概念，是真假还是id等。
2、隐式的表达
```
if(!done)
while(a < b)

// 而非
if(done === false)
while((a <b) === true)
```
3、将复杂判定条件赋值给变量，或生成布尔函数模式
```
if(a && b && c && d && e)

// 变量or函数
const done = a && b && c && d && e;
if(done)
```
4、更偏向于使用肯定到布尔表达式
5、使用括号使表达式更清晰
```
if(a < b === c === d)
//或者
if((a < b) === (c === d))
```
6、理解各语言内，布尔的求值，判定方式。易错的、短路求值等

**复合语句，语句块**

**空语句**

**深层嵌套**

重新设计if esle执行的判断，重构提取子程序基本能解决该问题。
1、重复判定的处理
2、转换 if else
3、转换 case
4、将深层嵌套代码提取子程序
5、使用状态变量重写
6、明确退出，简化逻辑

**控制结构与复杂度**

控制结构之所以重要，因为其直接影响代码的整体复杂度。程序复杂度是一个衡量指标，为了理解程序，你必须在同一时间记住的智力实体数量。这类智力游戏是编程中最难的方面之一，需要比任何活动都专心。被打断的成本代价很高。

复杂度的重要性：
『有能力的程序员会充分的意识到自己大脑容量是多么有限，所以会非常谦卑的处理编程任务』

**降低复杂度的原则**
1、通过脑力练习提升你的脑力水平
2、降低你应用程序复杂度

度量复杂度的方法：
1、从1开始，往下运行程序
2、遇到下列或者相关关键词，加1：if、while、repeat、for、and、or
3、case内每一种情况加1
```
if((status = SCUESS) && done || (!done && (numLines >= maxLines)))
// 1、if +1、&& +1、|| +1、&& +1  5个决策点
```
0-5 子程序可能还可以
6-10 需要简化子程序
10+ 需要优化与拆分

其他类型：数据量、控制结构嵌套层次、代码行数、变量跨度和生存期等。



![image](http://image.freefe.cc/2019-06-16_10-28-00.png)



