# 与DOM的区别

出于性能和跨浏览器兼容性的考虑，React实现了一套与浏览器无关的事件和DOM系统。同时，React的实现去除了一些浏览器DOM实现比较糟糕的方面，具体如下。

- 所有DOM的property和attribute，包括事件handler，都是驼峰式的，与标准的Javascript保持一致。这一点我们与HTML标准不同，因为HTML标准在这方面是不一致的。只有一种例外，`data-*`和`aria-*`属性遵循HTML标准，应当只包含小写字母。

- `style`属性接受的不是CSS字符串，而是一个Javascript对象，其属性名也是驼峰式的。这与DOM的`style`Javascript属性保持了一致性，更有效率，并且防范了XSS安全漏洞。

- 由于`class`和`for`是Javascript的关键字，JSX中为内置DOM节点实现的组件应当对应使用属性名`className`和`htmlFor`（比如`<div className="foo" />`）。而自定义元素应当直接使用`class`和`for`（比如`<my-tag class="foo" />`）。

- 所有的事件对象都遵循W3C标准，所有事件（包括submit）的冒泡也正确遵循W3C标准。详情请参阅[React事件系统](/react/event-system.md)。

- `onChange`事件的行为如你所望：任何时候表单字段改变了，就会触发这个事件，而不是像DOM一样在blur的时候才触发。目前的浏览器行为实际上是不当的，我们之所以要不一样是因为React依赖于`onChange`事件来实时响应用户的输入。详情可参考[React表单](/react/forms.md)。

- 表单输入属性规定为`value`、`checked`等，`<textarea`也不例外。

# 特殊的非DOM attribute

除了常规的attribute外，React提供了一些在DOM里面不存在的attribute：

- `key`：是一个可选的、唯一的标识符。在`render`过程中，组件可能会打乱，根据diff算法可能会销毁或重新创建。持续的赋予`key`属性能够保证组件不用销毁或重新创建。

- `ref`：组件通过`ref`保持对该元素在虚拟DOM里面的引用。

- `dangerouslySetInnerHTML`：提供直接插入原始HTML的能力，主要是为了跟一些DOM字符串操作库协作。