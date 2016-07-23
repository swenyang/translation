# Symbol类型 Symbol Type

## Symbol类型

Symbol是ES6引入的一种新原始数据类型，是独一的、不可修改的值。它是JavaScript语言的第七种数据类型，前六种是：undefined、null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。Symbol可以作为对象属性的标识符，解决之前标识符都是字符串容易冲突的问题。Symbol有个可选的描述信息，但仅用作调试。

- ES6

	```js
	Symbol("foo") !== Symbol("foo")
	const foo = Symbol()
	const bar = Symbol()
	typeof foo === "symbol"
	typeof bar === "symbol"
	let obj = {}
	obj[foo] = "foo"
	obj[bar] = "bar"
	JSON.stringify(obj) // {}
	Object.keys(obj) // []
	Object.getOwnPropertyNames(obj) // []
	Object.getOwnPropertySymbols(obj) // [ foo, bar ]
	```


- ES5

	```js
	// no equivalent in ES5
	```

## 全局Symbols

可以定义全局的Symbol，通过独一的key来索引。

- ES6

	```js
	Symbol.for("app.foo") === Symbol.for("app.foo")
	const foo = Symbol.for("app.foo")
	const bar = Symbol.for("app.bar")
	Symbol.keyFor(foo) === "app.foo"
	Symbol.keyFor(bar) === "app.bar"
	typeof foo === "symbol"
	typeof bar === "symbol"
	let obj = {}
	obj[foo] = "foo"
	obj[bar] = "bar"
	JSON.stringify(obj) // {}
	Object.keys(obj) // []
	Object.getOwnPropertyNames(obj) // []
	Object.getOwnPropertySymbols(obj) // [ foo, bar ]
	```


- ES5

	```js
	// no equivalent in ES5
	```
