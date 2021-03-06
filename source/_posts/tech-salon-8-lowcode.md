---
title: 技术沙龙第八期-低代码
date: 2022-01-17
categories: 应用
---

渲染：


给大家分享下低代码。也是最近我看到的一篇从实现上讲低代码的好文，整理了下，加上了一些自己的看法，分享给大家

渲染：


那么什么是低代码呢？低代码是指通过少量代码就可以快速生成应用程序的开发平台。.通过可视化进行应用程序开发的方法，使具有不同经验水平的开发人员可以通过图形化的用户界面，使用拖拽组件和模型驱动的逻辑来创建网页和移动应用程序。

渲染：


也就是说相对明确的一点是 可视化 是低代码唯一不可缺少的功能

渲染：


那么对于实现可视化编辑来说，必要条件又是什么呢？

渲染：


是声明式

渲染：


与声明式相对的，还有一种编码模式叫命令式。下面先来解释下什么是声明式、什么是命令式？

渲染：


比如我们要实现一个蓝色的方块，就拿我所用的前端的语言来说吧，HTML、CSS就是声明式的，来实现它，我只需要这么写

渲染：

![](1.jpeg)


渲染：


而如果换成使用命令式的javascript来实现，则可能会这么写

渲染：

![](2.jpeg)


渲染：


两种方式最终展现效果是一样的，但我们可以看到，这两种代码在实现思路上有本质区别

渲染：


声明式直接描述最终效果，不关心如何实现。
而命令式则关注如何实现，明确怎么一步步达到这个效果。

渲染：


回到可视化编辑器的角度看，它们的最大区别是：声明式可以直接从展现结果反向推导回源码，而命令式则无法做到反向推导

渲染：


反向推导是编辑器必备功能，比如编辑器里的常见操作是点选这个区块，然后修改它的颜色，在这两种代码中如何实现？

渲染：


如果是声明式的 HTML CSS，可以直接改style的background值，而基于 Canvas 的命令式代码则无法实现这个功能，因为无法从展现找到实现它的代码，因为命令式代码实现同样效果的可能方式是无数的，除了前面的示例，下面这段代码也可以实现一样的效果：画一条长100，粗100的线段，在最终视觉呈现上也可以看做是一个矩形。

渲染：

![](3.jpeg)


渲染：


因此我可以简单得到一下结论：命令式代码无法实现可视化编辑，而可视化编辑是低代码唯一不可少的功能，所以所有低代码平台必然只能采用声明式代码，这也是为什么所有低代码平台都会有内置的DSL。

渲染：


这些声明式语言有以下优点：
1、容易上手，因为描述的是结果，语法可以做得简单，非研发也能快速上手 HTML 及 SQL。
2、支持可视化编辑，微软的 HTML 可视化编辑 FrontPage 在 1995 年就有了，现在各种 BI 软件可以认为是 SQL 的可视化编辑。
3、容易优化性能，无论是浏览器还是数据库都在不断优化，比如可以自动改成并行执行，这是命令式语言无法自动实现的。
4、容易移植，容易向下兼容，现在的浏览器能轻松渲染 30 年前的HTML，而现在的编译器没法编译 30 年前的浏览器引擎代码。

渲染：


而这些语言的缺点是：
1、只适合特定领域，命令式的语言比如 JavaScript 可以用在各种领域，但 HTML CSS 只适合渲染文档及界面，SQL 只适合做查询。
2、灵活性差，比如 SQL 虽然内置了很多函数，但想只靠它实现业务是远远不够的
3、调试困难，遇到问题时如缺乏工具会难以排查，如果你在Firefox出现前开发过页面就会知道，由于IE6没有开发工具，编写复杂页面体验很差，遇到问题要看很久代码才发现是某个标签没闭合或者 CSS 类名写错了。

渲染：


4、强依赖运行环境，因为声明式只描述结果而不关注实现，因此强依赖运行环境，但这也带来了以下问题：
- 1)、功能取决于运行环境，比如浏览器对 CSS 的支持程度决定某个属性是否有人用，虽然出现了新的CSS提案，但 Firefox 和 Safari 都不支持，而且上手成本太高，预计以后也不会流行。
- 2)、性能取决于运行环境，比如同一个 SQL 在不同数据库下性能有很大区别。
- 3)、对使用者是黑盒，使用者难以知道最终实现，就像很少人知道数据库及浏览器的实现细节，完全当成黑盒来使用，一旦遇到性能问题可能就不知所了。
- 4)、技术锁定，因为即便是最开放的 HTML 也无法解决，很多年前许多网站只支持 IE，现在又变成了只支持 Chrome，微软和 Opera 在挣扎了很多年后也干脆直接转向用 Chromium。同样的即便有 SQL 标准，现在用的 Oracle/SQL Server 应用也没法轻松迁移到 Postgres/MySQL 上。低代码行业未来也一样，即便出了标准也解决不了锁定问题，更有可能是像小程序标准那样发展缓慢，功能远落后于微信。

渲染：


因为低代码就是一种声明式编程，所以这些声明式优缺点，其实就是低代码的优缺点。

渲染：


了解了声明式，下面来说说低代码的实现方案

渲染：


以前端的实现来说，其核心是界面渲染。前面提到前端 HTML CSS 可以看成一种描述界面的低代码 DSL，因此前端界面实现低代码会比较容易，只需要对 HTML CSS 进行更进一步封装，定义JSON schema。比如用类似如下的方式来描述页面：

渲染：


![](4.jpeg)

渲染：


这里大家几乎全都使用 JSON主要是两方面原因：
1. 低代码平台编辑器几乎都是基于 Web 实现，JavaScript 可以方便操作 JSON。
2. JSON 可以支持双向编辑，它的读取和写入是一一对应的。

渲染：


因此界面呈现上的低代码实现起来，我们只需要丰富物料库，通过拖拽这些物料拼出想要的东西后生成json描述即可。

渲染：


再来说说交互逻辑的实现。

渲染：


前面说到前端界面低代码是比较容易，但交互及逻辑处理却很难低代码化，目前常见实现有三种方案：

1、使用图形化编程；

2、固化交互行为；

3、使用 JavaScript；

渲染：


先说第一种图形化编程，这是非常自然的想法，既然低代码的关键是可视化，那直接使用图形化的方式编程不就行了？

渲染：


但我们发现这么做局限性很大，本质的原因是命令式的代码无法可视化。即便我们将循环、分支判断、或操作符等等这些抽象为一块块的积木，我们也难以像拼接积木一样得到我们想要的东西，因为积木拼接这种方式只适合用来实现简单的逻辑，对于复杂的交互逻辑非常难以实现。

渲染：


再来说固化交互行为，如果是面向特定领域，低代码平台可以先将这个领域难以图形化的逻辑预置好，让使用者只需做简单的处理，使用的时候只需要调整参数就行。当然这个方案最大的缺点是灵活性很低。

渲染：


因此要实现更灵活的控制，还是得支持第三个方案：JavaScript，目前很多低代码平台只在界面编辑提供可视化编辑，一旦涉及到交互就得写 JavaScript，但该方案脱离了低代码范畴、不是低代码。

渲染：


下面来看个实例，以阿里的datav中的蓝图编辑器为例，它就是同时支持了3种方案进行互补：

渲染：

![](5.jpeg)


渲染：


一些简单逻辑用户可以自己通过蓝图编辑器去添加然后串并联这些节点来实现。对于使用场景较多的一些较复杂的行为可以内置固话。而对于较复杂的逻辑用户可以自己通过js处理。

渲染：


最后总结一下吧。其实可以看出在定制化较强的业务中低代码可以说是毫无用武之地，但基于特定领域或方向的低代码平台还是很有意义的。将其作为一类工具，趁手时就用。