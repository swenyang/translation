# 协调算法（Diff算法）

React的关键设计决策是让API看起来像是每次更新都能重新渲染整个app。这使编写应用变得更加简单，但这也是很难驾驭的一个挑战。这边文章主要介绍了我们怎么用强大的启发法将一个O(n^3)复杂度的问题转变为O(n)的。

## 动机

计算将一棵树转换成另一棵树的最小操作数是一个复杂的、研究成熟的问题。[前沿的算法](http://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)显示这个问题大约是O(n^3)的复杂度，其中n是树种节点的数量。

这意味着显示1000个节点就需要大概要10亿次的比较，对于我们使用React的情形来说来说，代价太大了。要正确地认识这个数字，我们要知道现在的CPU一秒钟大概能执行30亿条指令。所以即使我们用性能最优的实现，我们也不能在一秒钟之内计算出diff。

由于找不到最优的算法，我们基于两个假设的启发下实现了一个非最优的O(n)算法：

- 两个相同类的组件将会产生相似的树，而两个不同类的组件将会产生不同的树。
- 为多次渲染中稳定存在的元素分配一个唯一的key是可能的。

在实践中我们发现，对于几乎所有的案例这些假设都不可思议的快。

## 逐对的diff

要对树做diff，我们首先要对两个节点进行diff。节点的diff有三种情况：

### 节点有不同的类型

如果节点的类型不同，React会认为他们是两颗不同的子树，摒弃之前的然后生成、插入新的节点。

```js
renderA: <div />
renderB: <span />
=> [removeNode <div />], [insertNode <span />]
```

对于自定义组件来说也是一样的逻辑。如果组件是不同的类型，渲染时React不会尝试去匹配它们，直接从DOM中移除原有的节点再插入新节点。

```js
renderA: <Header />
renderB: <Content />
=> [removeNode <Header />], [insertNode <Content />]
```

这个进阶知识点是React的diff算法既快又精准的一个非常重要的方面。它为快速裁剪树中大的部分、快速聚焦到可能相似的部分提供了非常好的启发。

`<Header>`元素生成的DOM树不太可能和一个`<Content>`元素相似。所以React没有花时间去尝试匹配两棵树，而是重新渲染一棵树。

可以推论的是，如果在两次render之间的相同位置都有一个`<Header>`，很有可能会出现相似的树，这种情况就值得去匹配。

### 相同类型的DOM节点

当比较两个DOM节点时，我们会查看两个节点的attributes然后决定哪个可以在线性时间内改变。

```js
renderA: <div id="before" />
renderB: <div id="after" />
=> [replaceAttribute id "after"]
```

样式方面我们没有使用难以理解的字符串，而是使用一个键-值对的对象。这样我们仅仅更新改变过的属性就可以了。

```js
renderA: <div style={{color: 'red'}} />
renderB: <div style={{fontWeight: 'bold'}} />
=> [removeStyle color], [addStyle font-weight 'bold']
```

attributes更新之后，我们会递归地更新所有子节点。

### 相同类型的自定义组件

如果两个自定义组件的类型是相同的，由于组件是有状态的，不能简单地直接用新建组件。React会把所有新建组件的attributes传递给原有组件，然后调用原有组件的`componentWillReceiveProps()`和`componentWillUpdate()`。

原有组件现在就是可以操作的了。它的`render()`方法会被调用，diff算法被应用在新的结果和旧的结果上。

## 逐列表的diff

### 问题案例

为了做子节点的更新，React采用了非常简单的方法。它同时遍历子节点的列表，一旦有变化就生成一个修改。

例如，你可以在后面插入一个元素：

```js
renderA: <div><span>first</span></div>
renderB: <div><span>first</span><span>second</span></div>
=> [insertNode <span>second</span>]
```

在前面插入一个元素就可能出问题，React会认为两个节点都是span，然后进入修改模式：

```js
renderA: <div><span>first</span></div>
renderB: <div><span>second</span><span>first</span></div>
=> [replaceAttribute textContent 'second'], [insertNode <span>first</span>]
```

有很多算法试图找到转换元素列表需要的最小操作集合。[莱文斯坦距离](https://en.wikipedia.org/wiki/Levenshtein_distance)能通过元素插入、删除和替换在O(n^2)的复杂度下找到最小集合。但即使我们使用莱文斯坦距离，当一个节点移动到另一个地方时算法也找不出来，如果要使用算法找出这类节点复杂度就更高了。

### key属性

为了解决这个看似难以解决的问题，我们引入了一个新的可选属性`key`。你可以为每个子节点分配一个用以匹配的键。如果你指定了一个键，React就能够用哈希表在O(n)的复杂度内完成插入，删除，替换和移动。

```js
renderA: <div><span key="first">first</span></div>
renderB: <div><span key="second">second</span><span key="first">first</span></div>
=> [insertNode <span>second</span>]
```

在实践中，找一个键是不难的。大部分情况下，你要显示的元素已经有了一个唯一的id。如果没有，你可以通过添加一个新的ID属性，或者哈希部分内容来生成一个键。注意键只需要对相邻兄弟节点唯一，不需要全局唯一。

## 权衡

要注意的是调解算法是一个实现细节。React可以对每一个action都重新渲染整个app，得到的结果也会相同。为了让常规应用场景运行的更快，我们会定期的优化启发法。

在当前的实现中，你可以通过key表明一颗子树在相邻节点间移动了，但是你如果你移动到其他地方（非相邻节点）key的作用就失效了，这种情况下算法将会重新渲染整个子树。

因为我们依赖于两个启发，所以如果背后的假设失效了，性能就会大打折扣。

1. 算法不会尝试去匹配不同组件类的树。如果你的两个组件有非常相似的输出，最好是写成同一个组件。在实践中，我们也没有发现这样会出问题。

2. `key`应当是稳定存在、可预测和唯一的。不稳定的键（比如通过`Math.random()`生成的）将会导致不必要地重新创建许多节点，而这将进一步引发性能退化、子组件里面的`state`丢失。