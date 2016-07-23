# 性能检测工具

React的运行出奇的快。但也存在一些场景，你需要极致地优化app的性能，为此React提供了`shouldComponentUpdate`钩子以让你给React Diff算法提供优化的提示。

为了让用户能概览app总体的性能情况，`ReactPerf`是一个能告诉你你应当优化哪些地方的分析工具。

可以参阅这两篇[Benchling工程团队](http://benchling.engineering/)的文章来获得关于性能工具更深度的知识：[Performance Engineering with React](http://benchling.engineering/performance-engineering-with-react/)和[A Deep Dive into React Perf Debugging](http://benchling.engineering/deep-dive-react-perf-debugging/)。

## 开发版本 vs. 生产版本

如果你在对React app进行基准测试或查看性能问题，确保你是在用压缩过的生产版本。开发版本包括了一些有用的额外警告，但也会因为开销而更慢一点。

但是，这篇文章要介绍的性能工具只能在开发版本的React使用。所以，这个分析工具只能推断你的app里面*相对*比较消耗性能的部分。

## 常用的API

此文档中的`Perf`是通过`require('react-addons-perf')`引用的，且只能在开发模式下的React中使用。在你打生产包时不要引用它。

### `Perf.start()` and `Perf.stop()`

开始/结束性能测量。在这期间的React操作会被记录，以便分析。占无关紧要的时间的操作将被忽略。

结束之后，你需要通过`Perf.getLastMeasurements()`（下面会介绍）来获得测量结果。

### `Perf.printInclusive(measurements)`

打印总体消耗的时间。如果没有传递参数，默认为从上次记录开始的所有测量。这个函数将在控制台打印排版好的信息，如下：

![perf-inclusive](/assets/images/perf-inclusive.png)

### `Perf.printExclusive(measurements)`

`Exclusive`时间不包括挂载组件消耗的时间：处理props，`getInitialState`，调用`componentWillMount`和`componentDidMount`等。

![perf-exclusive](/assets/images/perf-exclusive.png)

### `Perf.printWasted(measurements)`

**分析工具中最有用的部分。**

`Wasted`时间是指花在组件上、但组件实际没有渲染任何东西的，比如`render`一直没有变化，所以也不需要处理DOM。

### `Perf.printOperations(measurements)`

打印所有底层的DOM操作，比如"set innerHTML"和"remove"。

![perf-dom](/assets/images/perf-dom.png)

### `Perf.printDOM(measurements)`

这个方法已经被重命名为上一段的`printOperations()`。目前`printDOM()`还是作为别名存在，但调用会产生废弃的警告，以后会被移除。

## 高级API

上面提到的print函数使用`Perf.getLastMeasurements()`来格式化打印结果。

### `Perf.getLastMeasurements()`

从上一次的开始/结束测量会话中获得关于性能测量的不透明的数据结构。你可以保存这个变量，以供前面所提到的方法分析上次测量。

不要依赖于这个函数返回值的数据结构，因为它可能在小的版本也会改变。如果返回值成为了一个可用的公共API，我们将会更新文档。