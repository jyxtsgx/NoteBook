原文链接：[Frameworks without the framework](https://svelte.technology/blog/frameworks-without-the-framework/)

## 框架之外：为什么我们不能更早的想到这个？
毫不费劲的通过vanilla javascript来编织一个重度程序是一件不可能的事，但编译器可以做到这样

我们已经给用户带来太多的代码了。和其他的前端开发者一样，我一直在努力否认这个事实，总觉得在页面加载100kb大小的javascript文件是很轻松的事，就像一张差不多大小的jpg图片！这之中最重要的是页面交互性能。

我错大发了，100kb的js根本无法和100kb的jpg相等。不仅仅因为网络传输时间影响了你的js启动，还有浏览器的解析和评估脚本时也会大大的延迟。在移动端，这些时间被压缩得很短。

如果你依然无法认同这个问题，就请在推特上关注[Alex Russell](https://twitter.com/slightlylate)，他最近都没有在[各种框架的社区里去结交更多的朋友了](https://twitter.com/slightlylate/status/728355959022587905)。但他没有做错，而且提出了替代Angular, React和Ember的使用方案——[Polymer](https://www.polymer-project.org/1.0/)——在前端的世界还没有被很好的推动，但绝不是因为没有市场前景。

## 框架实际为我们解决了什么问题
一个普遍的观点是：框架有助于我们更好的去整合、管理复杂的代码。类似于虚拟DOM的差异化处理，框架能帮我们抽象出所有繁琐的实现细节，让我们能更专注于业务表现。

相反，React正因为能使开发者更容易管理复杂的业务逻辑而理所当然的获得了成功，框架是构建你设想的工具，而不是代码。

假定，框架无法在浏览器上正常运行会怎样呢？如果，它是将你的代码转换成原生的javascript推送到浏览器，就像Babel一样呢？你将不再需要为程序执行支付高昂的代价，你的app会因为代码和浏览器之间抽象的消失而6到飞起.

## Svelte 简介
Svelte正是一款这样的新的框架。你可以使用HTML, CSS, Javascript来编写你的组件，并且在构建过程中，Svelte将会分别把它们编译成微小独立的javascript模块，通过静态分析组件的模板应用，我们可以确保浏览器尽可能少的工作。

Svelte实现的MVC方案在压缩以后仅有3.6kb。对比于React，在不计算其余app业务代码的情况下，React和ReactDOM都已经有45kb了。浏览器至少需要耗费10倍以上的时间才能有效地执行React，而Svelte能轻松完成交互。

在你的app上线运行后，依据js框架标准，Svelte是更有效率的，比React快，比Vue快，比Angular, Ember, Preact, Riot, Mithril都要快。它和Inferno在竞争最快的地位，毕竟 [Dominic Gannaway](https://twitter.com/trueadm) 是一个巫师，他让Inferno成了世界上最快的UI框架。(Svelte在移除元素的时候稍慢，我们正在努力改善这一块)

## 但那些都不是重要的事
嗯，重要的是——性能真的很重要。这个方法最令人兴奋的是，在web开发中我们终于可以解决一些棘手的问题了。

考虑到操作性：想要使用命令 `npm install cool-calendar-widge` 来将日历组件添加到你的应用？你能这样做的前提是你想要的组件正是在你的app框架基础上构建的，如果 `cool-calendar-widget` 是用React构建而你却用着Angular，嗯哼，会很难啃的。但是，如果组件是采用Svelte构建，那无论你喜欢任何技术，在编译之后，你都能自由使用这个组件。

代码拆分：这是个很好的主意(只加载用户需要的初始化视图的代码，其余的代码在之后的触发中拉取更新)。但这里有一个问题——即使你仅想加载使用1个React组件，而不是100个，你依然需要完整的加载整个React。对于Svelte来说，代码拆分将会更加有效，因为框架嵌入在组件之中，而组件已足够小。

最后，我作为一个开源框架的维护者而做了大量的工作：你的用户总是希望他们的功能可以尽量优先，也低估了不需要这些功能的用户去维护的代价。框架的作者必须考虑用户的期望来平衡这个项目的长期发展。这是非常困难的，因为无法预料，更无法说明爆发性增长带来的后果。只能借助软技能来告诉人们(他们可能因为那个功能而积极的推广你的框架或工具)，这个功能不够重要！但是对于像Svelte这样的方案来说，添加功能并不会给用户带来任何的代价，只要你不使用。因为那些功能只要不是必须的，你就可以不编译实现。