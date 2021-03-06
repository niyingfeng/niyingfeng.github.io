---
title: 浅谈前端工程化
date: 2020-04-11
tags:
- 工程化
- 前端工程化
---

![image](http://image.freefe.cc/202004111008.jpeg)

在前端开发的角度看工程化，相对来说缺乏一定的经验标准。在此简单聊聊自身前端工程化的概念和理解。若有错误，欢迎指正。

<!-- more -->
## 软件危机与软件工程

简单的了解下早期由于软件问题引发的真实案例。

> **Therac-25医疗事故：**
> 加拿大原子能公司生产的放射线疗法机器，其软件系统由于未处理好多进程时序的冲突问题，导致多起医疗事故。患者接受了高出正常剂量的百倍辐射，造成多起死亡或严重灼伤事件。

> **美国银行信托软件系统开发：**
> 82年美国银行规划发展信托软件系统，原计划预算2千万美元，耗时9个月，于84年底前完成。实际至87年3月，期间投入6千万美元，最终因系统不稳定而放弃。导致被迫转移340亿美元信托资金，失去6亿美元的信托商机。

> **阿丽亚娜-5运载火箭爆炸事件：**
> 96年6月，阿丽亚娜-5运载火箭首次测试发射，在发射后37秒被迫自行引爆，原因由于64位的运算错误地变为16位的运算，造成程序崩溃，火箭感测角度失常，从而触发自毁装置的启动。

以上软件问题均出现在计算机诞生早期，而在如今互联网崛起的时代，问题似乎越发严峻。单以安全为例，不管我们的防御如何的升级与完善，相关的事件问题确越来越多。

> Google遭受黑客入侵的极光运动、索尼PlayStation和索尼影视黑客事件、Adobe攻击事件、Carbanak组织入侵银行、乌克兰电网入侵、雅虎数据泄露、万豪酒店数据泄露等。

或许你会觉得，以上软件问题和网络安全事件，与工程化或者说软件工程又有什么关系呢？

其实很简单，所有的各类软件或网络安全问题，本质上都指向了一个根本原因：**日益庞大的软件系统，其复杂度已经远超我们人脑可以直接控制的程度。**

于是北大西洋公约组织，在1968年创造了软件危机一词，来定义软件项目开发中的预算、周期以及质量引发的问题。同时召集了数十位业界顶尖的编程人员、计算机科学家和工业届巨头，讨论制定摆脱软件危机的对策，也是首次提出软件工程）的概念。

### 软件危机（Software crisis）

![image](http://image.freefe.cc/202004111002.jpeg)

软件危机的概念：在软件开发与维护过程中暴露出来的一系列的严重问题，导致直接影响软件的使用寿命甚至夭折。

对应具体问题主要有以下类型：

- 项目超出预算。
- 项目超过排期。
- 软件质量低落。
- 软件不符合需求。
- 项目无法管理，代码难以维护。

是不是与我们现在项目中遇到的问题大同小异？

### 软件工程

> 软件工程：研究和应用如何以系统性的、规范化的、可定量的过程化方法去开发和维护软件，以及如何把经过时间考验而证明正确的管理技术和当前能够得到的最好的技术方法结合起来的学科 - 维基百科。

因软件危机而生的软件工程，主要由以下两个构面：（深入了解学习具体可参见《代码大全》学习）

- 软件开发技术：开发方法、工具、开发环境
- 软件项目管理：软件度量、项目估算、进度控制、人员组织、配置管理、项目计划

简单说完软件危机和软件工程，那边开始简单说说前端工程化的概念。

## 前端工程化

随着互联网的飞速的迭代，前端也从早期的页面项目形态到日趋复杂的软件形态，项目复杂度呈指数级上升，也就给前端开发者带来了不可避免的软件危机的问题。

- 接手的项目代码质量是不是想口吐芬芳？
- 多人协作开发是不是混乱不堪？
- 公共方法、函数、样式、组件等是不是使维护无从下手？
- 提交、测试、上线流程是不是存在各类安全分险问题？
- ...

作为一线开发者，我们肯定遇到过以上类似情况，这也是目前软件开发赤裸裸的实现。那么对于前端工程化，就是希望能更好的解决类似的问题。

- 如何保证项目的协作开发效率？
- 如何保证项目的代码质量与迭代开发质量？
- 如何保证项目的可复用性、可维护性和可扩展性？
- 如何保证项目重复工作的效率与准确性？
- 如何保证项目开发、上线流程的低风险？
- ......

结合工程化的概念来说，**前端工程化的定义就是借助软件工程的技术、方法和思想，来优化前端侧的流程、效率和规范等，使用系统性、规范化、可定量的前端工程实践，结合项目自身，构建出相对较优的工程实践方案**。

参考网上将前端工程化分四个能力点：规范化、模块化、组件化、自动化，个人还是比较认同。

### 规范化

![image](http://image.freefe.cc/202004111018.jpeg)

无规矩不成方圆，对于编程这件事情，我们需要建立许多的规范标准，有效的执行才能确保我们项目各方面质量。而之所以将规范列为首要条件，不仅仅因为规范是最重要的一点也是最难做好的一点。

框架层面不管我们使用的是React、VUE或者是jQuery，样式上不管是用Sass、Less还是原生css，只要在早期的基础架构上，积极的制定好初步的规范，持之以恒去坚持和完善，那么我们的项目就可以具有生生不息的生命力。而遵守规范也是作为一个合格程序员最基础的条件。唯一的准则是你可以去质疑或者优化规范，但是没有打破之前，你不能不遵守规范。

以下简单梳理下个人能考虑到的一些关于开发规范方面的点：

#### 分支规范

分支规范是处理多人协同开发的关键点，没有绝对的正确答案，具体依照项目自身复杂程度以及基础环境，来评估最佳的分支实践。

目前大致有以下类型分支形态：

- 1、主干开发工作流：主干开发主干发布。
- 2、功能分支工作流：功能分支开发，主干发布。
- 3、分支开发分支发布工作流：功能分支开发，功能分支发布，合入主干。
- 4、GitFlow：Master、Develop、Feature、Release、Hotfix 多功能分支动态协作方式。
- 5、Github Flow：Master 与 Feature 分支的简单模型。
- 6、GitLab Flow：与Github Flow类似，添加Pre-Production 和 Production 分支进行环境部署处理

> 注意分支规范等同时，需要遵守标签的规范。

#### 编码规范

编码规范的目的就是提高代码的可读性和可维护性，确保自己或者他人维护或者扩展代码的统一性，降低复杂度，也避免语言上的一些细节问题等。前端侧包含但不仅限于 HTML规范、CSS规范、JS规范、VUE&React规范、TS规范等。

#### 架构规范

前期保证简单易扩展的基础架构模式，开发人员需严格遵守现有的结构、组织以及构建模式。在需要升级优化架构时，进行基础评估以及各使用方确认，在保证兼容，不降低质量的前提下进行架构升级以满足业务的复杂度。

#### 提交与CR规范

代码的提交前置检测与CR，是我们确保项目代码质量最重要的环节。代码规范是否统一，是否可能含有隐性的逻辑问题，代码质量是否符合标准等各方面的质量控制均在这两个环节进行把控。自动化风格检测，自评与他评等，是作为我们开发者自身去确保代码质量最基础的标准。

#### 其他

其他方面还包括对于自身项目设计评审规范、单测标准规范、提测流程规范、上线流程规范等标准化的建立与执行等。

所有环节均以标准化、规范化进行执行，方能确保我们的项目不至于快速陷入项目的软件危机之中。

### 模块化

![image](http://image.freefe.cc/202004111032.jpg)

万物皆模块！在前端工程化的不断进化中，我们逐渐将前端侧所接触的一切内容，行为以及资源，均作为模块的概念进行抽象与管理，达到我们统一管理项目资源的目的。

对项目进行逻辑细化、管理拆分是模块化概念不变的主题，旨在降低我们大脑的思维逻辑在同一时间需要思考的复杂度级别，从而提高项目的简单可维护性。管理复杂度是我们最终目标。

#### JS模块化

随着ES规范的不断完善，JS的模块化历程也逐渐的统一。从早期的AMD、CMD转化到了ES6 Module，从标准规范转到了ES6规范，成为了语言的特性。

CommonJS：主要用于Node侧，每个文件均为独立的模块，均含有自身的作用域，通过require来引入内置、自定义以及第三方模块。由于偏向服务端侧规范，以同步模式进行引入，相对来说不适用于浏览器侧模式。

AMD：RequireJS的模块化规范产出，非同步模块的加载，以回调函数方式执行。define来定义模块以及require来加载模块使用模块。需提前加载所有依赖而非按需加载，依赖前置，提前执行。

CMD：依赖就近，延迟执行。主要为SeaJS推广的模块定义规范，集成了CommonJS与AMD的一些特新，同时支持同步与异步加载。

ES6 Module：作为JS内置的语言特性，相对于CommonJS

- CommonJS为运行时加载，ES6 Module为编译时输出接口
- CommonJS加载整个模块的所有接口，ES6 Module可以独立输出其中一个接口
- CommonJS输出值的拷贝，ES6 Module输出值的引用

当然，借助于强大的Webpack我们能很好的将不同类型的模块化规范的JS文件进行结合构建，共存使用。

JS的模块化历程也算是前端模块化历程的缩影，不断的尝试以及最后的统一，感谢ESMA，当然也感谢WebPack。

#### CSS模块化

借助于CSS预编译工具的广泛使用，如Sass、Less、Stylus等，前端侧相对较为死板的CSS也变得无比灵活，在支持变量、嵌套、混合等高级的能力支持，使得CSS的细化拆分变得容易，从而使得前端开发者能更好的以模块化的思维来对项目的CSS进行统筹管理。极大简化了项目CSS的开发与维护复杂度。

但是以上的工具纯粹解决的只是样式模块化复用，编程能力弱的问题，缺无法解决以下问题：

- 样式全局污染，以及各类权重覆盖问题
- 命名混乱的冲突
- CSS模块引入相互影响，作用域问题无法解决
- 压缩侧对于冗长的样式名等无能为力

于是乎，BEM风格解决方案、CSS Modules 模块化方案等开始应运而生，各类方案各有利弊，依据项目与团队本身进行评估选择。

BEM风格：Block、Element和Modifier通过命名规范来规避以上问题，前提是在各方开发者遵守既定规范的前提下。

CSS Modules：类似的仍是基于命名层面处理，对应组件内模块化样式进行特定规范标识，从而解决上述问题。

当然，没有银弹，我们要做好的就是以最合适的方案解决我们项目的问题。

#### 资源模块化

万物皆模块的基本表达，不管是图片、数据还是其他各类资源，借助于WebPack的强大能力，将所有项目相关资源视为模块进行管理。

> webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

### 组件化

![image](http://image.freefe.cc/202004111023.jpeg)

组件化相对于模块化来说，是一个更高纬度的颗粒化，而在前端侧，组件化主要指的UI组件化。如果说各类构建工具推动了前端模块化的支持与发展，那边前端流程的框架便推动了前端组件化概念思维的大爆发。

组件化的目标与模块化类似，主旨便是降低复杂度。将特定的逻辑、模板以及样式独立封装，具有可复用性和低耦合性的基础UI模块。简单来说无非就是隐藏自身内部逻辑，减小编程开发中时间点所需要思考的复杂度。

对于项目来说，一般对于组件的划分模式较多，自己喜欢将其划分为基础UI组件，业务逻辑组件以及页面组件。

- **基础UI组件：** 相对项目来说最底层支持配置的组件，具有统一的业务逻辑和行为等，比如项目自定义Button、Input等，广泛使用的一些基础UI组件库（Ant Design、ElementUI等）。在编写基础UI组件时，需要明确与具体业务逻辑剥离，专注于所定义的基础组件的逻辑、交互和行为，按需进行配置化支持。基础组件更偏向于复用性。

- **业务逻辑组件：** 在基于基础组件的前提下，结合项目业务进行组件化，对于页面级别进行拆分和组件化，目标仍是封住逻辑，降低项目内页面开发复杂度。相对而言，业务组件更偏向于逻辑封装和管理。

- **页面组件：** 页面级别对于基础组件与业务逻辑组件的统筹管理，在页面级别则不在深入具体的细节交互逻辑。

当然组件类型的拆分编程思维，万千世界各有不同，每位开发者对于组件化思维的考虑均有不同，以符合项目自身出发。

### 自动化

![image](http://image.freefe.cc/202004111033.jpg)

前端自动化主要指前端工程上各阶段使用自动化工具来提高效率与准确性的方式。如代码风格与质量检测，构建、部署、测试、发布等大量重复性和大数量级工作的处理，实现自动化检测、评估与运行。

自动化概念设计到开发者整个开发流程的各阶段，具有非常重要的席位，下面简单介绍一些实用的自动化工具：

- 自动化代码检测：不用说，强大的套餐，ESLint、StyleLint、HTMLHint等，以项目代码规范进行静态资源检测配置，进行代码前置检测。
- 自动化构建工具：毫无疑问，目前WebPack算是一统天下，并且其加载器、插件几乎集成了所有基础的自动化处理工具的能力。grunt -> gulp -> webpack 也算是前端构建历史的迭代历程了。 
- 自动化测试工具：除了大量流程的测试框架如mocha、jest等，还有如测试平台karma、断言工具库chai，以及无头浏览器PhantomJS，真实浏览器工具puppeteer等。
- 自动化部署：此处工具依赖各开发者公司的几次发布平台，一般均有固定的支持的模式。（如jenkins支持等）


### 总结

最后总结来说一下，前端工程化所需要解决的问题，也无非就是软件工程所要解决的难点。软件工程中没有银弹，也没有绝对的好与坏，只有是否适用和正收益。

前端工程化问题无非从软件规范、开发思维以及开发工具，认真做好每一步，尽量减弱破坏项目规则的每一个可能的点来加强我们项目的健壮性。当然对于非常多的项目，实现以上所有的点太过于理想化，所以要评估我们项目工程化程度的前提是，我们是否确定已经评估我们所需要项目的健壮程度，而后在进行评估做到怎样程度的工程化。

最后结合百度工程化的一些评估要求，简单产出相对可量化评估的偏前端工程化Map：

- 需求阶段：迭代管理、需求管理、BUG管理
- 开发阶段：设计评审、分支管理、项目规范、编码规范、提交规范、需求关联
- 准入阶段：CodeReview、单元测试、增量代码扫描、集成构建方式、核心功能准入测试
- 测试阶段：手动测试用例、自动化测试用例、全量代码扫描、性能评估、功能回归
- 发布阶段：部署产物规范、资源缓存规范、页面监控检测

最最后，我们需要记住任何行为，均有其正面与反面意义，我们需要评估的是其正收益是什么！
