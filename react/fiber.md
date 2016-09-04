# React Fiber架构

## 引言

React Fiber是React核心算法的重写，目前还在开发中。它是React团队过去两年研究的高峰。

React Fiber的目标是增强对于某些领域的适应性，比如动画、布局和手势等。它的重要特性是**增量渲染**：将渲染事务分块并分布到多个帧去完成的能力。

其他关键特性包括有新的更新时，暂停、终止或重用事务的功能；为不同类型的更新设置优先级的功能；还有新的并发原语。

## 关于本文档

Fiber引入了几个新颖的概念，单单看代码是很难理解的。本文档起初是React项目在逐步实现Fiber的时候记的笔记集，随着慢慢扩充，我发现这对于其他人来说可能也是很实用的资源。

我会尽可能的使用简单的语言，通过显示的定义一些术语来避免行话。如果可能，我也会尽可能链接到外部资源。

注意我并不在React团队里面，不代表任何官方的发言。**这不是官方文档**。不过我请教了React团队的一些成员来检查文档的准确性。

这也是一项进行中的工作。**Fiber也是一个开发中的项目，在完成之前有可能还会有重大的重构**。所以我在此为它的设计写的文档也是进行中的。非常欢迎提出优化和建议。

我的目标是读完这篇文档后，你能够理解Fiber足够深来读懂[它的实现](https://github.com/facebook/react/commits/master/src/renderers/shared/fiber)，最终甚至能够返回来给React做一些贡献。

## 必要的知识

我强烈建议在继续看本文档之前，你先熟悉一下以下资源：

- [React组件，元素和实例](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html) -”组件“是经常提起的术语。对这些术语有坚实的认知是非常重要的。
- [协调算法](/react/diff-algorithm.md) - React协调算法的高阶阐述。
- [React基本理论概念](https://github.com/reactjs/react-basic) - 不带实现细节的React理论模型阐述。其中一些内容可能在第一次阅读时没什么意义，但随着时间推移会证明非常有用。
- [React设计原则](https://facebook.github.io/react/contributing/design-principles.html) - 特别注意其中的Scheduling环节，它很好的诠释了React Fiber诞生的原因。

## 回顾

如果你还没阅读“必要的知识”环节，请确认阅读一下。

在我们学习新的东西前，我们回顾一些概念。

### 什么是协调算法

- 协调算法(reconciliation)

    React用以比较两棵树的算法，其决定哪些部分需要更改。

- 更新(update)

    即渲染React app的数据发生的一个改变。通常是`setState`的结果。最终会导致重新渲染。

React API的核心思想是让更新能引发整个app及时的重新渲染。这将允许开发者作出声明式的推断，而不用担心app从一个状态切换到另一个状态时过渡的效率（A到B，B到C，C到A等等）。

实际上，对于每一次改变都重新渲染app只对一些小型应用有用；现实中，这是极度浪费性能的。React使用了一些优化，使得保持高性能的同时能够达到类似重新渲染整个app的效果。这些优化就是**协调算法**的一部分。

协调算法是基于通常被称为“虚拟DOM”技术的。一个高阶的描述是：当你渲染一个React app的时候，会生成app描述的节点树并保存在内存中。然后这个树被输出到渲染环境——比如，在浏览器应用中，它被翻译为一堆DOM操作。当一个app更新时（一般是通过`setState`），就产生了一棵新树。新树和旧树会进行比较，计算需要哪些操作来更新app。

虽然Fiber是协调算法的推倒重写，关于[React文档](/react/diff-algorithm.md))中的高阶知识还是一致的。主要的两点是：

- 不同类型的组件很大程度上将会产生不同的树。React将不会比较他们，而是直接用新的完全替代旧的。
- 列表的diff使用key。Key应当是稳定、可预测、并且唯一的。

### 协调算法 vs 渲染

DOM只是React可以输出的渲染环境之一，其他的一些主要目标包括通过React Native输出到原生的iOS和Android视图。（这也就是为什么“虚拟DOM”是有点用词不当的）

React支持这么多目标的原因是因为它将协调算法和渲染设计成了不同的阶段。协调算法负责计算一棵树哪些部分更改了；渲染器则利用该信息来实际更新app。

这个分离意味着React DOM和React Native能共享React核心提供的协调算法，而使用它们各自的渲染器。

Fiber重构了协调器。Fiber不是渲染需要首要考虑的，但渲染器需要改变来支持（并且利用）新的架构。

### 调度(Scheduling)

- 调度(scheduling)

    决定事务什么时候执行的过程。

- 事务(work)

    必须执行的计算。事务一般是由update引起的（比如`setState`）。

React的[设计原则文档](https://facebook.github.io/react/contributing/design-principles.html#scheduling)在这方面写的很好，我引用如下：

>在当前的实现中，React递归遍历节点树，在一个tick内调用整棵新树的渲染函数。但是未来有可能推迟一些更新来避免掉帧。
>
>这是React设计的一个常见主题。一些流行的库采用"push"方法，在新的数据就绪的时候就执行计算。React仍然使用"pull"方法，计算可以推迟到需要的时候才执行。
>
>React不是通用的数据处理库。它是用以建立用户界面的库。我们认为在一个app中，知道计算是相关还是不相关的是独一无二的重要。
>
>如果某些东西超出了屏幕显示，我们可以延迟它相关的所有逻辑。如果数据到达的比帧率要快，我们可以合并和批处理更新。我们可以优先处理用户交互的事务（比如点击按钮引起的动画），次要处理没那么重要的背景事务（比如渲染刚从网络上返回的数据）来避免掉帧。

关键点在于：

- 在用户界面中，没必要每一个更新都立即执行；实际上这样做是很浪费的，导致掉帧，降低用户体验。
- 不同类型的更新具有不同的优先级——一个动画更新需要比数据源的更新更快的完成。
- 一个基于push的app需要应用（你，程序员）来决定怎么调度事务。基于pull的app允许框架(React)变得智能化，帮你完成这些决策。

React现在没有很重要地利用调度；一个更新会引发整个子树被立即重新渲染。革新React的核心算法来充分利用调度是Fiber背后的驱动想法。

<hr/>

现在我们已经准备好了深入Fiber的实现。下一节比我们前面讨论的都要更偏技术一点。继续之前，确认你学习好了前面的知识点。

## 什么是Fiber？

我们将要讨论React Fiber架构的核心部分。Fibers是比应用开发者通常思考的部分要低很多的底层抽象。如果你发现很难理解它，不要丧气。多尝试几次，最终你就能理解。（如果你最后懂了，请提些建议来改进这节内容）

出发！

<hr/>

我们前面已经确定了Fiber的主要目标是使React能够充分利用调度。详细来说，我们需要能够：

- 暂停事务，在一段时间后再接着执行。
- 分配不同类型事务的优先级。
- 复用之前已完成的事务。
- 当事务不再需要时，终止该事务。

为了实现上面的任意一个功能，首先我们需要分解事务为若干单元的方法。在某种意义上那就是一个fiber。一个fiber代表了事务的一个单元。

更进一步之前，我们重温一下[React组件是数据的函数](https://github.com/reactjs/react-basic#transformation)的概念，通常表达为：

```js
v = f(d)
```

它表明渲染一个React app类似于调用一个函数，这个函数体里面包含了对其他函数的调用，以此类推。这个类比在思考fiber的时候很有帮助。

计算机跟踪一个程序执行的方式是[调用栈](https://en.wikipedia.org/wiki/Call_stack)。当一个函数执行的时候，栈上面就会添加一个新的堆栈结构。这个堆栈结构代表了那个函数执行的事务。

处理用户界面时，问题是如果太多的事务同时执行，它可能导致动画掉帧，看起来卡顿。还有，其中的一些事务是不需要的，因为它可能被最近的更新取代了。这就是UI组件和函数的比较分解的地方，因为通常组件比函数有更详细的关注点。

最新的浏览器（和React Native）实现了API来定位这个问题：`requestIdleCallback`调度一个低优先级的函数在空闲阶段执行，`requestAnimationFrame`调度一个高优先级的函数在下一个动画帧执行。问题在于，要使用这些API，你需要将渲染事务分解为增量单元的方法。如果你仅依赖于调用栈，它将会一直工作直到栈变空。

如果我们能自定义调用栈的行为来优化UI渲染该多好啊？如果我们能主动中断调用栈，手动操作堆栈结构该多好啊？

那就是React Fiber的目的。Fiber是专门为React组件实现的堆栈重构。你可以把单个fiber当做一个**虚拟的堆栈结构(virtual stack frame)**。

重构堆栈的好处是你能[保存堆栈结构在内存里](https://www.facebook.com/groups/2003630259862046/permalink/2054053404819731/)，然后用你想要的方式和时机去执行它。这点对于实现我们要的调度功能非常重要。

除了调度，手动操作堆栈结构也使的一些潜在特性变得可能，比如并发和错误边界。我们将会在未来的章节讨论这些话题。

下一节，我们将讨论更多关于fiber的结构。

### 一个fiber的结构

***注意：随着我们讨论更多的实现细节，某些东西被改变的概率也会上升。如果你发现了任何错误或过时的信息，请帮忙提交一下PR。***

具体来说，一个fiber是包含了一个组件及其输入、输出信息的JavaScript对象。

一个fiber对应了一个堆栈结构，同时它也对应了一个组件实例。

下面是fiber的一些重要字段。（这个列表不是完全的）

#### `type`和`key`

fiber的`type`和`key`的作用和React元素一样。（实际上，一个fiber从组件创建时，这两个字段会直接复制过来）

fiber的`type`描述了它对应的组件。对于复合组件，`type`就是复合函数或组件的class。对于宿主组件（`div`, `span`等），`type`是一个字符串。

从概念上来说，`type`是执行会被堆栈结构跟踪的函数（即在`v = f(d)`里面的）。

除了`type`之外，`key`是在协调算法中用来决定fiber是否可以重用的字段。

#### `child`和`sibling`

这些字段指向其他的fiber，描述的是fiber的递归树结构。

子fiber(`child` fiber)指的是组件的`render`方法返回来的值。比如在下面的样例中`Parent`的子fiber就是`Child`。

```js
function Parent() {
  return <Child />
}
```

兄弟fiber(`sibling` fiber)指代的是`render`方法返回多个子节点的情况（Fiber中的新特性！）：

```js
function Parent() {
  return [<Child1 />, <Child2 />]
}
```

兄弟fiber形成一个单链表，链表头就是第一个子节点。在上面的例子中，`Parent`的子节点是`Child1`，`Child1`的兄弟节点是`Child2`。

回顾我们前面的函数类比，你可以把子fiber当做是一个[尾调用函数](https://en.wikipedia.org/wiki/Tail_call)。

#### `return`

返回fiber(`return` fiber)是指程序处理完当前的fiber后应当处理的下一个fiber。理论上和一个堆栈结构的返回地址一样。也可以认为是父fiber。

如果一个fiber有多个子fiber，每个子fiber的返回fiber都是它的父fiber。所以在我们上一个例子中，`Child1`和`Child2`的返回fiber都是`Parent`。

#### `pendingProps`和`memoizedProps`

概念上来说，props就是一个函数的参数。一个fiber的`pendingProps`会在它开始执行处设置，`memoizedProps`则会在执行结尾处设置。当到来的`pendingProps`和上一个`memoizedProps`相等时，它意味着fiber的上一次输出可以重用，避免不必要的事务。

#### `pendingWorkPriority`

指示fiber代表的事务优先级的一个数字。[React优先级](https://github.com/facebook/react/blob/master/src/renderers/shared/fiber/ReactPriorityLevel.js)模块列举了不同的优先级和它们代表的意义。

除了用0表示`NoWork`之外，越大的数字代表了越低的优先级。例如，你可以用下面的函数来检查一个fiber的优先级是不是不低于给定的等级：

```js
function matchesPriority(fiber, priority) {
  return fiber.pendingWorkPriority !== 0 &&
         fiber.pendingWorkPriority <= priority
}
```

这个函数仅仅是作说明用，它实际上不属于React Fiber代码库。

调度器使用这个优先级字段来搜索下一个要执行的事务。搜索算法会在将来的章节讨论。

#### `alternate`

- `flush`

    冲洗一个fiber就是渲染输出结果到屏幕上。

- `work-in-progress`

    进行中的fiber代表一个未处理完毕的fiber，概念上来说就是一个还未返回的堆栈结构。

在任意一个时刻，一个组件实例最多有两个fiber对应着它：当前fiber，冲洗的fiber和进行中的fiber。

当前fiber的替代(`alternate`)就是进行中的fiber，进行中的fiber的替代就是当前fiber。

一个fiber的替代是通过`cloneFiber`函数懒创建的。`cloneFiber`会尝试重用fiber的替代（如果存在）来最小化分配空间，而不是总创建新的对象。

你应当把`alternate`当做是一个实现细节，但它在代码里面出现了很多次，所以值得在这里讨论。

#### `output`

- `host component`

    宿主组件(`host component`)是React应用的叶节点。它们是跟特定的渲染环境相关的（比如，在浏览器应用中，宿主组件是指`div`, `span`等）。在JSX中，它们是用小写字母的tag名称表示的。

概念上来说，一个fiber的输出结果就是一个函数的返回值。

每个fiber最终都会有输出结果，但是输出结果是仅由叶节点的宿主组件创建的。然后输出结果会向上转移到整个树。

输出结果就是最终交给渲染器的东西，然后渲染器能够将更改冲洗到渲染环境。定义输出结果怎么样创建和更新就是渲染器的职责了。

## 未来的章节

上面就是至今为止的内容了，但是这个文档还远未接近完成。未来的章节将会描述贯穿整个生命周期和更新使用的算法。将覆盖的主题包括：

- 调度器怎么样找到下一个要执行的事务单元
- 整个fiber树的优先级怎么跟踪和传播
- 调度器怎么样知道什么时候暂停和继续事务
- 事务是怎么冲洗和标记为完成的
- 附带后果（比如声明周期方法）是怎么样工作的
- 什么是协同程序，和它是怎么样用来实现一些特性比如上下文(context)和布局(layout)的

本文翻译自<https://github.com/acdlite/react-fiber-architecture>，欢迎斧正。