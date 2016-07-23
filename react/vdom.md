# React DOM（虚拟DOM）术语

在React术语中，要区分五种非常重要的核心类型：

- ReactElement / ReactElement工厂
- ReactNode
- ReactComponent / ReactComponent类

## React Elements

`ReactElement`是React中的原始类型，它有四个属性：`type`，`props`，`key`和`ref`。它没有方法，`prototype`上也没有任何属性。

可以通过`React.createElement`创建一个`ReactElement`：

```js
var root = React.createElement('div');
```

要往DOM里面插入一个新的React树，需要创建`ReactElement`然后将其和一个常规DOM `Element`（`HTMLElement`或者`SVGElement`）传递给`ReactDOM.render`方法。不要混淆`ReactElement`和`Element`。`ReactElement`是DOM `Element`轻量的、无状态的、不可更改的、虚拟的展现。它是一个虚拟的DOM。

```js
ReactDOM.render(root, document.getElementById('example'));
```

要往一个DOM元素添加`properties`，可在第2个参数传递一个属性对象，第3个参数传递子节点。

```js
var child = React.createElement('li', null, 'Text Content');
var root = React.createElement('ul', { className: 'my-list' }, child);
ReactDOM.render(root, document.getElementById('example'));
```

如果使用JSX，`ReactElement`可以自动创建，下面代码等同上面：

```js
var root = <ul className="my-list">
             <li>Text Content</li>
           </ul>;
ReactDOM.render(root, document.getElementById('example'));
```

### React Elements工厂

一个`ReactElement`工厂就是能根据特定的`type`生成`ReactElement`的函数。React内置了一个帮助函数来创建工厂，如下：

```js
function createFactory(type) {
  return React.createElement.bind(null, type);
}
```

上述代码能创建一个方便的简写，而无需总输入`React.createElement('div')`。

```js
var div = React.createFactory('div');
var root = div({ className: 'my-div' });
ReactDOM.render(root, document.getElementById('example'));
```

对于常规的HTML tag来说，React已经内置了工厂：

```js
var root = React.DOM.ul({ className: 'my-list' },
    React.DOM.li(null, 'Text Content')
);
```

如果你在使用JSX就不需要使用工厂。JSX已经提供了方便的简写来创建`ReactElement`。

## React Nodes

一个`React Node`可能是以下类型：

- `ReactElement`
- `string`（也就是`ReactText`）
- `number`（也就是`ReactText`）
- `ReactNode`数组（也就是`ReactFragment`）

这些就作为`ReactElement`子节点属性（children）的参数，最终形成一颗`ReactElement`树。

## React Components

在React的世界，你可以只使用`ReactElement`，但是如果要充分利用React的优势，你将需要利用`ReactComponent`来封装内置`state`的组件。

一个`ReactComponent`类就是一个Javascript类（或者构造函数）：

```js
var MyComponent = React.createClass({
  render: function() {
    ...
  }
});
```

当构造函数被调用的时候，返回的对象至少要包含一个`render`方法，这个对象就被引用为`ReactComponent`。

```js
var component = new MyComponent(props); // never do this
```

除非是测试用，否则基本上你不需要去调用这个构造函数。React会自动帮你完成。相反，你应当传递`ReactComponent`类给`createElement`来得到一个`ReactElement`。

```js
var element = React.createElement(MyComponent);
```

或者使用JSX：

```js
var element = <MyComponent />;
```

当这个组件被传递给`ReactDOM.render`时，React会替你调用构造函数，并创建一个`ReactComponent`返回。

```js
var component = ReactDOM.render(element, document.getElementById('example'));
```

如果你一直使用相同的`ReactElement`类型和相同的容器DOM `Element`来调用`ReactDOM.render`，它将一直返回同一个实例，这个实例是状态性的。

```js
var componentA = ReactDOM.render(<MyComponent />, document.getElementById('example'));
var componentB = ReactDOM.render(<MyComponent />, document.getElementById('example'));
componentA === componentB; // true
```

这就是为什么你不需要去构造实例的原因。实际上，`ReactElement`在被构造之前是一个虚拟的`ReactComponent`。新的和老的`ReactComponent`可以进行比较，然后决定是创建一个新的`ReactComponent`实例还是复用之前的实例。

`ReactComponent`的`render`方法应当返回另一个`ReactElement`，这样才能把组件组合在一起。最后渲染解析为一个`string` tag的`ReactElement`，它会实例化一个DOM `Element`实例并插入到文档中。

React 0.14引入了无状态的函数组件作为替代定义组件的一种方式。它不是一个类，仅仅是一个接受`props`参数并返回一个`ReactElement`的简单函数。

## 正式的类型定义

### 入口

```js
ReactDOM.render = (ReactElement, HTMLElement | SVGElement) => ReactComponent;
```

### Nodes和Elements

```js
type ReactNode = ReactElement | ReactFragment | ReactText;

type ReactElement = ReactComponentElement | ReactDOMElement;

type ReactDOMElement = {
  type : string,
  props : {
    children : ReactNodeList,
    className : string,
    etc.
  },
  key : string | boolean | number | null,
  ref : string | null
};

type ReactComponentElement<TProps> = {
  type : ReactClass<TProps> | ReactFunctionalComponent<TProps>,
  props : TProps,
  key : string | boolean | number | null,
  ref : string | null
};

type ReactFragment = Array<ReactNode | ReactEmpty>;

type ReactNodeList = ReactNode | ReactEmpty;

type ReactText = string | number;

type ReactEmpty = null | undefined | boolean;
```

### Classes和Components

```js
type ReactClass<TProps> = (TProps) => ReactComponent<TProps>;

type ReactComponent<TProps> = {
  props : TProps,
  render : () => ReactElement
};

type ReactFunctionalComponent<TProps> = (TProps) => ReactElement;
```