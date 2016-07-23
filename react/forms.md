# React表单

与其他的原生组件不同，`<input>`、`<textarea>`和`<option>`可以被用户的交互修改。这些组件也提供了更多的接口，使得管理响应用户交互的表单更加容易。

## 交互式的props

表单组件支持几种可以被用户交互影响的`props`：

- `value`，`<input>`和`<textarea>`组件支持
- `checked`，`<input>`组件的`checkbox`和`radio`类型支持
- `selected`，`<option>`组件支持

HTML中，`<textarea>`的值通过子元素来设置。但React中通过`value`来设置。

表单元素允许通过设置`onChange`属性来监听所有的变化。`onChange`属性能跨浏览器地响应用户操作：

- `<input>`和`<textarea>`组件的`value`发生变化
- `<input>`组件的`checked`发生变化
- `<option>`组件的`selected`发生变化

跟其他所有DOM事件一样，`onChange`属性能被所有的原生组件支持，并且可以用作监听冒泡的change事件。

注意：对于`<input>`和`<textarea>`，`onChange`应当用来替代DOM的内置`oninput`事件handler。

## 受控的组件

**受控**的`<input>`拥有`value`属性。渲染受控的`<input>`时会显示`value`属性的值。

```js
render() {
    return <input type="text" value="Hello!" />
}
```

用户的输入将不会影响受控组件，因为React已经将组件的值设置为`Hello!`了。要根据用户输入更新值，应当使用`onChange`事件：

```js
constructor(props) {
    super(props)
    this.state = {value: 'Hello!'};
}

handleChange(event) {
    this.setState({value: event.target.value})
}

render() {
    return (
        <input
        type="text"
        value={this.state.value}
        onChange={this.handleChange.bind(this)}
        />
    )
}
```

在例子中，我们通过更新`<input>`组件的`value`属性来接受用户的输入。这种模式更容易校验用户的交互。

受控的组件不维护自己的内部`state`，组件纯粹的通过`props`来渲染。

### Checkbox和Radio可能遇到的问题

需要注意的是，`checkbox`和`radio`修改时，React使用的是一个`click`事件而不是`change`事件。通常情况下这种行为是OK的，但如果在`change`事件handler调用了`preventDefault`时就不是了。即使`checked`状态翻转了，`preventDefault`也会阻止浏览器可视的更新input。这个问题可以通过两种方式解决：一是移除`preventDefault`的调用，二是在`settimeout`里面来翻转`checked`状态。

## 不受控的组件

`<input>`没有`value`属性时就是一个不受控的组件。

```js
render() {
    return <input type="text" />
}
```

这样将会渲染一个初始值为空的input。任何用户的输入都会立即显示在组件里。如果想要监听value的改变，可以跟受控元素一样，通过`onChange`事件来实现。

**不受控**的组件维护了自己的内部`state`。

## 默认值

如果想渲染一个初始值非空的组件，可以提供`defaultValue`属性，比如：

```js
render() {
    return <input type="text" defaultValue="Hello!" />
}
```

这几乎和上面提到的受控元素一样。

同样的，`<input type="checkbox">`和`<input type="radio">`支持`defaultChecked`，`<select>`支持`defaultValue`。

注意：`defaultValue`和`defaultChecked`只在第一次渲染使用，如果需要在后续的渲染中更新`value`，需要使用受控组件。

# 高级话题

## 为什么要使用受控组件

实际上，React的表单组件比如`<input>`解决了传统HTML表单不能解决的一个挑战。比如，在HTML中：

```js
<input type="text" name="title" value="Untitled" />
```

这将渲染一个初始值为`Untitled`的input。当用户更新输入后，节点的`value`属性将会改变，但是，`node.getAttribute('value')`仍然会返回初始化时的值`Untitled`。

与HTML不同，React组件必须及时地展现view任意时刻的状态，而不仅仅是初始化的时候。比如：

```js
render() {
    return <input type="text" name="title" value="Untitled" />
}
```

由于这个方法描述了view在任意时刻的状态，那么这个input的`value`就永远都是`Untitled`。

## 为什么要在textarea上用value属性

在HTML中，`<textarea>`的值通常用子元素来设置：

```html
<!-- antipattern: DO NOT DO THIS! -->
<textarea name="description">This is the description.</textarea>
```

对于HTML来说，这样方便地让开发者提供多行数据。但是，由于React就是Javascript，我们没有字符串的限制，可以用`\n`表示换行。对于`value`和`defaultValue`的存在，子元素方式就会变得很奇怪。因此，最好不要通过子元素的方式来设置`<textarea>`的值：

```html
<textarea name="description" value="This is a description." />
```

如果你坚持要用子元素的方式，它们将会和`defaultValue`一样。

## 为什么要在select上用value属性

在HTML的`<select>`中，选择的值是通过`<option>`的`selected`属性来设置的。React为了更好的操作组件，采用了如下的形式：

```html
<select value="B">
    <option value="A">Apple</option>
    <option value="B">Banana</option>
    <option value="C">Cranberry</option>
</select>
```

如果要实现一个不受控的组件，则使用`defaultValue`。

注意：你可以提供给`value`属性一个数组，这样允许选择`<select>`的多个值：`<select multiple={true} value={['B', 'C']}>`。

## 手动操作

如果需要手动的进行一个操作，你需要获得DOM节点的引用。比如，如果想要手动submit表单，一个方法是附加`ref`到`form`的元素上，并手动调用`form.submit()`。