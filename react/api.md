# 顶层的API

## React

`React`是React库的入口。如果你使用的是预先构建的包，它是一个全局变量；如果使用CommonJS模块，需要先`require()`。ES6语法用`import`。

### React.Component

```js
class Component
```

如果使用ES6的类来编写组件，`Component`应当是组件的基类。`Component`包含的API在下文将详细给出。

### React.createClass

```js
ReactClass createClass(object specification)
```

根据给的的`specification`创建一个组件类。一个组件必须实现`render`方法，并在这个方法返回**单一**的子节点。子节点可以有任意深度的子结构。组件与标准原型类的一个区别是你不需要用`new`来创建组件。

关于`specification`对象的更多信息可参阅[组件标准和生命周期](/react/component-specs-and-lifecycle.md)。

### React.createElement

```js
ReactElement createElement(
  string/ReactClass type,
  [object props],
  [children ...]
)
```

根据给定的`type`，创建并返回一个新的`ReactElement`。`type`参数可以是html的tag名称字符串（比如`'div''`,`'span'`等），也可以是`React.createClass`创建的`ReactClass`。

### React.cloneElement

```js
ReactElement cloneElement(
  ReactElement element,
  [object props],
  [children ...]
)
```

以给定的`element`作为初始值，复制并返回一个新的`ReactElement`。返回的元素拥有原来元素的props和新的props合并过的属性（浅拷贝）。新的子节点会替代原来的子节点。与`React.addons.cloneWithProps`不同，原元素的`key`和`ref`可以保留。

### React.createFactory

```js
factoryFunction createFactory(
  string/ReactClass type
)
```

返回一个能根据给定的`type`创建React元素的函数，跟`React.createElement`一样，`type`可以是html的标签（`'div'`，`'span'`等），或者是一个`ReactClass`。

### React.isValidElement

```js
boolean isValidElement(* object)
```

检查一个元素是否为合法的React元素。

### React.DOM

`React.DOM`为DOM组件封装了`React.createElement`方便的接口。仅当不使用JSX的时候才应该使用`React.DOM`。样例：`React.DOM.div(null, 'Hello World!')`。

### React.PropTypes

`React.PropTypes`用以验证传递给组件的`props`对象参数的合法性。

### React.Children

`this.props.children`的数据结构是不透明的，所以`React.Children`提供了一些工具来处理它。

#### React.Children.map

```js
array React.Children.map(object children, function fn [, object thisArg])
```

对children执行map操作，并返回一个新的数组。

#### React.Children.forEach

```js
React.Children.forEach(object children, function fn [, object thisArg])
```

与map类似，但是不返回数组。

#### React.Children.count

```js
number React.Children.count(object children)
```

返回children元素的个数。个数与`map`和`forEach`执行次数相同。

#### React.Children.only

```js
object React.Children.only(object children)
```

#### React.Children.toArray

```js
array React.Children.toArray(object children)
```

将`children`每个元素放到一个数组中并返回。

## ReactDOM

`react-dom`包提供了一些可以在app顶层访问的DOM相关方法，也用以安全访问React模型之外的东西。你大部分的组件都不需要使用这个模块。

### ReactDOM.render

```js
render(
  ReactElement element,
  DOMElement container,
  [function callback]
)
```

在指定的`container` DOM节点上渲染一个ReactElement，并返回组件的引用（无状态的函数组件将会返回`null`）。如果ReactElement之前已经在`container`渲染过，将会只更新这个组件，按需修改DOM来正确的展示React组件。如果提供了`callback`，组件渲染或更新完成后将执行它。

注意：
>`ReactDOM.render()`将会完全控制你传递的`container`节点的内容。第一次调用时，里面所包含的任何DOM节点都会被替换，后续为了高效的更新则使用React的DOM diff算法。
>
>`ReactDOM.render()`不会修改`container`本身，只会修改其子节点。以后可能会支持只插入组件到DOM节点，但是不会覆盖其原有子节点。
>
>`ReactDOM.render()`现在会返回根组件的引用，但是使用这个返回值是一个遗留问题，应当尽量避免，因为未来React可能在某些情况会进行异步渲染。如果你需要根组件的引用，最好是附加一个`ref`属性来获得。

### ReactDOM.unmountComponentAtNode

```js
boolean unmountComponentAtNode(DOMElement container)
```

从DOM中移除一个已挂载的React组件，并且清除它的事件处理和`state`。如果`container`中未挂载组件，调用这个函数将毫无影响。如果有组件卸载，将返回`true`；否则返回`false`。

### ReactDOM.findDOMNode

```js
DOMElement findDOMNode(ReactComponent component)
```

如果组件已经在DOM中挂载了，将返回对应的原生浏览器DOM元素。这个方法对于从DOM中读取数值非常有用，比如表单字段值和进行DOM测试。大部分情况下，你可以附加`ref`到DOM节点上来避免使用`findDOMNode`。当`render`返回`null`或`false`时，`findDOMNode`返回`null`。

注意：
>`findDOMNode()`是可以安全访问底层的DOM节点的。大部分情况下，不推荐使用此方法，因为这样破坏了组件的抽象性。
>
>`findDOMNode()`只对已经挂载过的组件有用（放在DOM中的），如果对一个未挂载的组件上调用此方法，会抛出异常。
>
>`findDOMNode()`不能用在无状态的函数组件上。

## ReactDOMServer

`react-dom/server`允许从服务器来渲染组件。

### ReactDOMServer.renderToString

```js
string renderToString(ReactElement element)
```

将一个`ReactElement`渲染为对应的HTML。这个函数仅能在服务器端调用，React将会返回一个HTML字符串。你可以用这个函数在服务器生成字符串，然后把渲染标记在首个请求中返回，既能快速加载页面，也能让搜索引擎抓取页面信息优化SEO。

如果在一个已经有服务器渲染标记的节点上调用`ReactDOM.render()`，基于性能优先的考虑，React会保持它，只添加事件handlers。

### ReactDOMServer.renderToStaticMarkup

```js
string renderToStaticMarkup(ReactElement element)
```

与`renderToString`类似，但是这个函数不会生成React使用的额外的DOM属性，比如`data-react-id`。在用React来做简单静态页面时比较有用，删除额外的属性可以节约大量的空间。
