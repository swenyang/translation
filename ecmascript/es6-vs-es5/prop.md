# 扩展对象属性 Enhanced Object Properties

## 属性简写

简写通用的对象属性定义语法。

- ES6

	```js
	obj = { x, y }
	```


- ES5

	```js
	obj = { x: x, y: y };
	```

## 计算属性名称

支持用计算的名字定义对象属性。

- ES6

```js
let obj = {
    foo: "bar",
    [ "baz" + quux() ]: 42
}
```


- ES5

	```js
	var obj = {
	    foo: "bar"
	};
	obj[ "baz" + quux() ] = 42;
	```

## 方法属性

支持用方法声明定义对象属性（普通函数和Generator函数均可）。

- ES6

	```js
	obj = {
	    foo (a, b) {
	        …
	    },
	    bar (x, y) {
	        …
	    },
	    *quux (x, y) {
	        …
	    }
	}
	```


- ES5

	```js
	obj = {
    foo: function (a, b) {
        …
    },
    bar: function (x, y) {
        …
    },
    //  quux: no equivalent in ES5
	```
