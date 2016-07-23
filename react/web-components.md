# React与Web Component

由于React和Web Component为了不同的目的而设计，所以对比两者必然会得出看似正确的结论。WebComponents为可复用组件提供了很强的封装性，而React则为保持DOM和数据的同步提供了一个声明式的库。这两个目的是互补的，工程师可以混合使用两个技术。作为开发者，你既可以自由地在你的WebComponents使用React，也可以自由地在React里面使用WebComponents，或者交叉使用两者。

## 在React里面使用Web Components

```js
class HelloMessage extends React.Component{
    render() {
        return <div>Hello <x-search>{this.props.name}</x-search>!</div>;
    }
}
```

注意：React Components和Web Components两种不同的组件系统在编程模式上也是不一样的，Web Components通常会提供一个命令式的API（比如，一个`video` web component通常会提供`play()`和`pause()`方法）。对于web component一些声明式的函数是可以通过属性访问的，上述代码OK；但对于命令式的API，你需要附加一个`ref`到组件上，然后直接操作DOM节点。如果使用的是第三方web components，推荐的解决方案是写一个React组件包装它。

此时，web component发出的事件可能不能在React渲染树中正确的传播，你需要在React组件中手动附加事件handler来处理这些事件。

## 在Web Components中使用React

```js
var proto = Object.create(HTMLElement.prototype, {
  attachedCallback: {
    value: function() {
      var mountPoint = document.createElement('span');
      this.createShadowRoot().appendChild(mountPoint);

      var name = this.getAttribute('name');
      var url = 'https://www.google.com/search?q=' + encodeURIComponent(name);
      ReactDOM.render(<a href={url}>{name}</a>, mountPoint);
    }
  }
});
document.registerElement('x-search', {prototype: proto});
```