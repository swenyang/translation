# 解构赋值 Destructuring Assignment

## 数组匹配

可以用直观灵活的数组解构赋值给单个变量。

- ES6

	```js
	var list = [ 1, 2, 3 ]
	var [ a, , b ] = list
	[ b, a ] = [ a, b ]
	```


- ES5

	```js
	var list = [ 1, 2, 3 ];
	var a = list[0], b = list[2];
	var tmp = a; a = b; b = tmp;
	```

## 简写属性匹配

可以用直观灵活的对象解构赋值给单个变量。

- ES6

	```js
	var { op, lhs, rhs } = getASTNode()
	```


- ES5

	```js
	var tmp = getASTNode();
	var op  = tmp.op;
	var lhs = tmp.lhs;
	var rhs = tmp.rhs;
	```

## 深度属性匹配

可以用直观灵活的对象解构赋值给单个变量。

- ES6

	```js
	var { op: a, lhs: { op: b }, rhs: c } = getASTNode()
	```


- ES5

	```js
	var tmp = getASTNode();
	var a = tmp.op;
	var b = tmp.lhs.op;
	var c = tmp.rhs;
	```

## 上下文匹配参数

函数调用时可以用直观灵活的对象、数组解构赋值给单个参数。

- ES6

	```js
	function f ([ name, val ]) {
	    console.log(name, val)
	}
	function g ({ name: n, val: v }) {
	    console.log(n, v)
	}
	function h ({ name, val }) {
	    console.log(name, val)
	}
	f([ "bar", 42 ])
	g({ name: "foo", val:  7 })
	h({ name: "bar", val: 42 })
	```


- ES5

	```js
	function f (arg) {
	    var name = arg[0];
	    var val  = arg[1];
	    console.log(name, val);
	};
	function g (arg) {
	    var n = arg.name;
	    var v = arg.val;
	    console.log(n, v);
	};
	function h (arg) {
	    var name = arg.name;
	    var val  = arg.val;
	    console.log(name, val);
	};
	f([ "bar", 42 ]);
	g({ name: "foo", val:  7 });
	h({ name: "bar", val: 42 });
	```

## 解构失败处理

解构失败不会报错，可以给予默认值。

- ES6

	```js
	var list = [ 7, 42 ]
	var [ a = 1, b = 2, c = 3, d ] = list
	a === 7
	b === 42
	c === 3
	d === undefined
	```


- ES5

	```js
	var list = [ 7, 42 ];
	var a = typeof list[0] !== "undefined" ? list[0] : 1;
	var b = typeof list[1] !== "undefined" ? list[1] : 2;
	var c = typeof list[2] !== "undefined" ? list[2] : 3;
	var d = typeof list[3] !== "undefined" ? list[3] : undefined;
	a === 7;
	b === 42;
	c === 3;
	d === undefined;
	```