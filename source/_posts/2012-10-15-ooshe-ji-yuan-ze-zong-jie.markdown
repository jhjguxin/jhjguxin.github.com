---
layout: post
title: "OO设计原则总结"
date: 2012-03-21 13:40
comments: true
categories: [专业]
tags: [dip,dry,isp,it,ocp,oo设计原则,srp]
---
## OO设计原则总结
<div> <wbr>设计原则是基本的工具，应用这些规则可以使你的代码更加灵活，更容易维护，更容易扩展。</wbr></div>
<div>基本设计原则</div>
<div><strong>封装变化（</strong><span style="font-family: Arial, Helvetica, sans-serif; color: #424a5e;">Encapsulate what varies</span><strong>）</strong></div>
<div><strong>面向接口编程而非实现(</strong>code to an interface rather than to an implementation<strong>)</strong></div>
<div><strong>优先使用组合而非继承(</strong>favor Composition over inheritance<strong>)</strong></div>
<div><strong>SRP(S</strong>ingle responsibility Principle<strong>) <wbr></wbr></strong></div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong> <span style="color: #ff0000;">单一职责</span>。系统中的每一个对象都应该只有1个单独的职责，而所有对象所关注的就是自身职责的完成。</div>
<div> <wbr>  <wbr> 每一个职责都是一个设计的变因，需求变化的时候，需求变化反映为类的职责的变化。当你系统里的对象都只有一个变化的原因时，你就已经很好的遵循了SRP原则了。<span style="color: #ff0000;">如果一个类承担了过多的职责，就等于把这些职责耦合在一起了。一个职责的变化就可能消弱或者抑制这个类其它的职责的能力。这种设计会导致脆弱的设计。</span>当发生变化时，设计会遭到意想不到的破坏。</wbr></wbr></div>
<div> <wbr>  <wbr> SRP让这个系统更容易管理和维护，因为不是所有的问题都耦合在一起。</wbr></wbr></div>
<div> <wbr>  <wbr> 内聚（Cohesion）其实是SRP原则的另外一个名字。你写了高内聚的软件其实就是很好的应用了SRP原则。 <wbr></wbr></wbr></wbr></div>
<div><strong>DRY(</strong>Don't repeat yourself Principle<strong>)</strong></div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong> <span style="color: #ff0000;">不要重复自己的工作</span>。通过抽取公共部分放置在一个地方来避免重复的代码或功能实现。</div>
<div> <wbr>  <wbr> DRY确保women代码容易维护和复用。确保每一个需求和功能在你的系统中只实现一次，否则就存在浪费！系统的用例不存在交集，所以我们的代码更不应该重复。从这个角度看DRY就不只是在说代码了。DRY关注的是系统内的信息和行为都放在一个单一的，明显的位置。</wbr></wbr></div>
<div> <wbr>  <wbr> <span style="color: #ff0000;">DRY原则：如何对系统职能进行良好的分割！职责清晰的界限一定程度上保证了代码的单一性。</span></wbr></wbr></div>
<div> <wbr>  <wbr> <wbr></wbr></wbr></wbr></div>
<div><strong>OCP(</strong>Open-Close Principle<strong>)</strong></div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong> <span style="color: #ff0000;">开闭原则</span><span style="color: #ff0000;">。</span>OCP关注的是灵活性，改动是通过增加代码进行的，而不是改动现有的代码。</div>
<div> <wbr>  <wbr> OCP的应用限定在可能会发生的变化上，通过创建抽象来隔离以后发生的同类变化。</wbr></wbr></div>
<div> <wbr>  <wbr> OCP传递这样一个思想：<span style="color: #9dbb61;">一旦你写出来可以工作的代码，就要努力保证这段代码可以一直工作。这就成了你的编码的一个底线。一旦我们的代码质量到了一个水平，我们就要尽最大努力保证代码质量不回退。这样就要求我们面对一个问题的时候不会使用凑活的方法来解决，或者说放任自流的方式来解决一个问题</span>（比如：代码添加了无数对特定数据的处理，特化的代码越来越多，代码意图开始含糊不清，这就开始质量退化了。）</wbr></wbr></div>
<div> <wbr>  <wbr> OCP背后的机制：封装和抽象。封闭是建立在抽象的基础上的，使用抽象获得显示的封闭。继承是OCP最简单的例子。除了子类化和方法重载我们还有一些更优雅的方法来实现比如组合。</wbr></wbr></div>
<div> <wbr>  <wbr> 那么如何在不改变源代码（关闭修改）的情况下更改它的行为呢？答案就是抽象。</wbr></wbr></div>
<div> <wbr>  <wbr> 正确的做法就是<span style="color: #ff0000;">开发人员仅对频繁变化的部分做出抽象。拒绝不成熟的抽象，这和抽象本身一样的重要</span>。</wbr></wbr></div>
<div> <wbr>  <wbr> OCP是OOD很多说法的核心，如果这个原则有效的应用，我们可以获得更强的可维护性 可重用性 灵活性 健壮性。。然而LSP是OCP成为可能的主要原则之一。</wbr></wbr></div>
<div><strong>LSP(Liskov</strong> <wbr>substitution<strong> <wbr></wbr></strong>Principle<strong>)</strong></wbr></div>
<div><span style="color: #ff0000;">子类必须能够替换基类</span>。LSP关注的是怎样良好的使用继承。必须清楚是使用一个Method还是要扩展它，但是绝对不是改变它。</div>
<div> <wbr>  <wbr>  <wbr>  <wbr> LSP让我们得出一个重要的结论：一个模型如果孤立的看，并不具有真正意义的有效性，模型的有效性只能通过它的客户程序来表现。必须根据设计的使用者做出的合理假设来审视它。而假设是难以预测的，知道设计臭味出现的时候才处理他们。</wbr></wbr></wbr></wbr></div>
<div><strong>DIP(</strong>Dependency-Inversion Principle<strong>)</strong></div>
<div><strong><span style="color: #ff0000;">依赖反转/依赖倒置</span>。</strong>高层模块不依赖底层模块 两者都应只依赖于抽象。</div>
<div> <wbr>  <wbr> 抽象不依赖于细节， 而细节依赖于抽象。</wbr></wbr></div>
<div> <wbr>  <wbr> 高层模块：包含了应用程序中重要的策略选择和业务模型。这些高层模块使其所在的应用程序区别于其他。</wbr></wbr></div>
<div><span style="color: #b5a5d5;">框架设计的核心原则</span>： 如果高层模块依赖底层模块，那么在不同的上下文中重用高层模块会变得十分困难。然而，如果高层模块不依赖于底层模块，那么高层模块就可以非常容易的被重用。</div>
<div> <wbr>  <wbr> 这里的倒置不仅仅指依赖关系的倒置同时也是接口所有权的倒置。</wbr></wbr></div>
<div> <wbr>  <wbr>Hollywood原则： Don't call us。 we will call you. 底层模块实现了在高层模块声明并被高层模块调用的接口。</wbr></wbr></div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong>DIP的简单的启发规则：依赖于抽象。程序汇总所有的依赖都应依赖于抽象类或接口。</div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong>如果一个类很稳定，那么依赖于它不会造成伤害。然而我们自己的具体类大多是不稳定的，通过把他们隐藏在抽象接口后面可以隔离不稳定性。</div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong></div>
<div><strong> <wbr>  <wbr>依赖倒置可以应用于任何一个类向另一个类发送消息的地方。。。（还不是很理解）</wbr></wbr></strong></div>
<div><strong> <wbr>  <wbr></wbr></wbr></strong>依赖倒置原则是实现许多面向对象技术多宣称的好处的基本底层机制，是面向对象的标志所在。 <strong> <wbr> <wbr></wbr></wbr></strong></div>
<div><strong>
</strong></div>
<div><strong>ISP(</strong>Interface Segregation Principle<strong>)</strong></div>
<div><strong><span style="color: #ff0000;">接口隔离原则</span>。</strong></div>
<div>使用多个专门的接口比使用一个单一的接口总要好：从一个客户类的角度来讲，一个类对另外一个类的依赖性应当是建立在最小的接口上。</div>
<div>如果接口不是高内聚的，一个接口可以分成N组方法，那么这个接口就需要使用ISP来处理一下了~~。</div>
<div> <wbr>  <wbr>  <wbr>  <wbr> 一个接口中包含了太多的行为时候，导致他们的客户程序之间产生不正常的依赖关系，我们要做的就是分离接口，实现解耦。使用了ISP后客户程序看到的是多个内聚的接口。</wbr></wbr></wbr></wbr></div>
&nbsp;
