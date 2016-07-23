# 组件标准和生命周期

## 组件标准

组件是一个包含`render`方法的对象，也可以选择性的包含其他生命周期方法。

- `render`

    ```js
    ReactElement render()
    ```

    `render()`方法是必须的，它可以读取`this.props`和`this.state`的数据并返回一个单一的子元素，子元素可以是DOM组件的虚拟表现（比如`<div />`）或者其他复合组件。

    如果什么都不想渲染就让`render()`返回`null`或者`false`。此时，`ReactDOM.findDOMNode(this)`也将返回`null`。

    `render()`应当是一个纯函数，它不会改变组件的`state`，每次调用都会返回相同的结果（`props`和`state`不变时），也不会对DOM进行读写，或者与浏览器交互（比如`settimeout`）。如果需要与浏览器交互，请在`componentDidMount()`或者其他生命周期方法进行。保持`render()`的纯粹，使得服务端渲染更易实践，组件本身更简单易读。

- `getInitialState`

    ```js
    this.state = {
        // write your state
    }
    ```

    设置组件的初始`state`，一般放在组件的构造函数`contructor(props)`进行。

- `getDefaultProps`

    ```js
    static defaultProps = {
        // write your default props
    }
    ```

    设置组件的初始`props`，上述写法需要开启ES6的类属性语法`babel-plugin-transform-class-properties`。

- `propTypes`

    ```js
    static propTypes = {
        // claim your prop types
    }
    ```

    声明组件`props`的字段和类型，这种写法同样要开启ES6类属性语法。

- `mixins`

    ES6语法不支持组件的`mixins`

- `statics`

    ```js
    class Example extends Component {
        static foo = () => {
           console.log('bar')
        }
    }

    Example.foo()
    ```

    声明组件的静态方法，ES6语法开启类属性语法可以很方便实现。

## 组件的生命周期方法

不同的生命周期方法会在组件的不同时刻调用。

- 挂载：`componentWillMount`

    ```js
    void componentWillMount()
    ```

    在客户端和服务端都只会调用一次，在第一次渲染发生前触发。如果在这个方法内部初始化或更新组件的`state`，`render()`会使用更新后的`state`，并且只渲染一次（尽管`state`更新了）。

- 挂载：`componentDidMount`

    ```js
    void componentDidMount()
    ```

    在客户端只会调用一次（服务端不会调用），在第一次渲染发生后触发。在这个方法里面，你可以访问到组件的子组件的`refs`（比如访问底层的DOM）。子组件的`componentDidMount()`会在父组件的`componentDidMount()`之前调用。

    如果你想整合其他Javascript框架，调用`settimeout`或者`setInterval`，或者发送AJAX请求，可以在这个方法进行。

- 更新：`componentWillReceiveProps`

    ```js
    void componentWillReceiveProps(object nextProps)
    ```

    当一个组件接收到新的`props`时被调用，在第一次渲染前不会触发。旧的`props`可以通过`this.props`访问，新的`props`可以通过参数`nextProps`访问。

    组件接收到新的`props`时，`render()`将会触发，在`render()`触发前可以在这个方法里通过`this.setState()`更新组件的`state`，此时不会触发额外的渲染。

    ```js
    componentWillReceiveProps(nextProps) {
      this.setState({
        likesIncreasing: nextProps.likeCount > this.props.likeCount
      })
    }
    ```

    注意：常见的一个错误是认为此方法被调用时组件的`props`一定改变了，实际上并不是。另一方面，`state`改变时，没有类似的`componentWillReceiveState`方法，因为`props`改变时可能会导致`state`改变，但是反过来不成立。如果需要在`state`改变时做一些操作，在`componentWillUpdate`进行。

- 更新：`shouldComponentUpdate`

    ```js
    boolean shouldComponentUpdate(object nextProps, object nextState)
    ```

    当接收到新的`props`或者`state`时，触发`render()`前这个方法会被调用。当然，如果是第一次渲染或者使用`forceRender()`，此方法不会被调用。

    当某些情况下`props`或`state`改变时，组件是不需要更新的，使这个方法`return false`可以避免触发渲染。如果`return false`，本次`render()`将会跳过，`componentWillUpdate`和`componentDidUpdate`也不会触发。

    默认情况下`shouldComponentUpdate`始终会返回`true`，避免一些不易察觉的bug。如果遇到了性能的瓶颈，特别是组件数量非常多时，可以使用`shouldComponentUpdate`来优化你的app。

- 更新：`componentWillUpdate`

    ```js
    void componentWillUpdate(object nextProps, object nextState)
    ```

    当接收到新的`props`或者`state`时，触发`render()`前这个方法会被调用。第一次渲染前不会触发。可以在此方法做一些更新前的准备。

    在这个方法里不能使用`this.setState()`，如果需要在`props`改变时更新`state`，请使用`componentWillReceiveProps`。

- 更新：`componentDidUpdate`

    ```js
    void componentDidUpdate(object prevProps, object prevState)
    ```

    当一个组件的更新推送到DOM后，此方法会被立即调用。第一次渲染不会触发。组件更新后，此方法是操作DOM的好时机。

- 卸载：`componentWillUnmount`

    ```js
    void componentWillUnmount()
    ```

    组件从DOM中卸载前，此方法会被调用。在这个方法中可以进行清理工作，比如清理计时器，或`componentDidMount`创建的DOM元素。