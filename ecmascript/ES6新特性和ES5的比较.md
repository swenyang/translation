# ES6新特性和ES5的比较
本文主要译自<http://es6-features.org>，简明的介绍了ECMAScript 6\(ES6\)的新特性和ECMAScript 5\(ES5\)实现的比较，其中一部分算是语法糖\(Syntactic Sugar\)，另外一部分则是ES5没有的新特性。如果不是很清楚，更详细的ES6语言规范可以参考[阮一峰的书](https://likebeta.gitbooks.io/es6tutorial/content/docs/regex.html)。

# 目录
* [常量 Constants](#constants)
	* [常量](#constants-1)
* [作用域 Scoping](#scoping)
	* [块级作用域变量](#scoping-1)
	* [块级作用域函数](#scoping-2)
* [箭头函数 Arrow Functions](#arrow)
	* [表达式](#arrow-1)
	* [声明式](#arrow-2)
	* [词法上的this](#arrow-3)
* [扩展参数处理 Extended Parameter Handling](#param)
	* [默认参数值](#param-1)
	* [剩余参数](#param-2)
	* [展开操作符](#param-3)
* [模板字面量 Template Literals](#template)
	* [字符串插值](#template-1)
	* [自定义插值](#template-2)
	* [访问原生字符串](#template-3)
* [扩展字面量 Extended Literals](#literals)
	* [二级制&十进制字面量](#literals-1)
	* [Unicode字符串&正则表达式字面量](#literals-2)
* [扩展正则表达式 Enhanced Regular Expression](#reg)
	* [正则表达式粘连匹配](#reg-1)
* [扩展对象属性 Enhanced Object Properties](#prop)
	* [属性简写](#prop-1)
	* [计算属性名称](#prop-2)
	* [方法属性](#prop-3)
* [解构赋值 Destructuring Assignment](#destruct)
	* [数组匹配](#destruct-1)
	* [简写属性匹配](#destruct-2)
	* [深度属性匹配](#destruct-3)
	* [上下文匹配参数](#destruct-4)
	* [弱化解析失败](#destruct-5)
* [模块 Modules](#modules)
	* [导入/导出值](#modules-1)
	* [默认导出&通配导出](#modules-2)
* [类 Classes](#class)
	* [类的定义](#class-1)
	* [类的继承](#class-2)
	* [从表达式继承类](#class-3)
	* [基类访问](#class-4)
	* [静态成员](#class-5)
	* [Getter/Setter](#class-6)
* [Symbol类型 Symbol Type](#symbol)
	* [Symbol类型](#symbol-1)
	* [全局Symbols](#symbol-2)
* [迭代器 Iterators](#iter)
	* [迭代器&for-of操作符](#iter-1)
* [Generators](#gen)
	* [使用迭代器协议的Generator函数](#gen-1)
	* [直接使用Generator函数](#gen-2)
	* [Generator匹配](#gen-3)
	* [Generator控制流](#gen-4)
	* [Generator方法](#gen-5)
* [Map/Set & WeakMap/WeakSet](#set)
	* [Set的数据结构](#set-1)
	* [Map的数据结构](#set-2)
	* [弱引用数据结构](#set-3)
* [类型数组 Typed Arrays](#array)
	* [类型数组](#array-1)
* [新的内建方法 New Built-In Methods](#built)
	* [对象属性赋值](#built-1)
	* [查找数组元素](#built-2)
	* [字符串反复](#built-3)
	* [字符串搜索](#built-4)
	* [数字类型检查](#built-5)
	* [数字安全检查](#built-6)
	* [数字比较](#built-7)
	* [数字截断](#built-8)
	* [确定数字符号](#built-9)
* [异步编程Promises](#promises)
	* [Promise的用法](#promises-1)
	* [Promise的结合](#promises-2)
* [元编程 Meta-Programming](#meta)
	* [Proxy](#meta-1)
	* [Reflection](#meta-2)
* [国际化及本地化 Internationalization & Localization](#inter)
	* [校对](#inter-1)
	* [数字格式化](#inter-2)
	* [货币格式化](#inter-3)
	* [日期/时间格式化](#inter-4)

<h2 id="constants" >常量 Constants</h2>
<h3 id="constants-1">常量</h3>
支持常量定义（不可修改的变量），即不可重新赋值的变量。注意：仅使变量本身不可修改，其赋值内容仍然可以修改（比如赋值内容为一个object，那么这个object仍然是可以修改自己属性的）。


- ES6

	```js
	const PI = 3.141593
	PI > 3.0
	```


- ES5

	```js
	//  only in ES5 through the help of object properties
	//  and only in global context and not in a block scope
	Object.defineProperty(typeof global === "object" ? global : window, "PI", {
	    value:        3.141593,
	    enumerable:   true,
	    writable:     false,
	    configurable: false
	})
	PI > 3.0;
	```


<h2 id="scoping">作用域 Scoping</h2>
<h3 id="scoping-1">块级作用域变量</h3>
let关键字实现块级作用域变量，不用像ES5一样作提前声明。

- ES6

	```js
	for (let i = 0; i < a.length; i++) {
	    let x = a[i]
	    …
	}
	for (let i = 0; i < b.length; i++) {
	    let y = b[i]
	    …
	}

	let callbacks = []
	for (let i = 0; i <= 2; i++) {
	    callbacks[i] = function () { return i * 2 }
	}
	callbacks[0]() === 0
	callbacks[1]() === 2
	callbacks[2]() === 4
	```

- ES5

	```js
	var i, x, y;
	for (i = 0; i < a.length; i++) {
	    x = a[i];
	    …
	}
	for (i = 0; i < b.length; i++)
	    y = b[i];
	    …
	}

	var callbacks = [];
	for (var i = 0; i <= 2; i++) {
	    (function (i) {
	        callbacks[i] = function() { return i * 2; };
	    })(i);
	}
	callbacks[0]() === 0;
	callbacks[1]() === 2;
	callbacks[2]() === 4;
	```

<h3 id="scoping-2">块级作用域函数</h3>
更方便的定义块级作用域函数。

- ES6

	```js
	{
	    function foo () { return 1 }
	    foo() === 1
	    {
	        function foo () { return 2 }
	        foo() === 2
	    }
	    foo() === 1
	}
	```

- ES5

	```js
	//  only in ES5 with the help of block-scope emulating
	//  function scopes and function expressions
	(function () {
	    var foo = function () { return 1; }
	    foo() === 1;
	    (function () {
	        var foo = function () { return 2; }
	        foo() === 2;
	    })();
	    foo() === 1;
	})();
	```

<h2 id="arrow">箭头函数 Arrow Functions</h2>
<h3 id="arrow-1">表达式</h3>
箭头函数类似于匿名函数，但是有更富表达性的闭包语法。

- ES6

	```js
	odds  = evens.map(v => v + 1)
	pairs = evens.map(v => ({ even: v, odd: v + 1 }))
	nums  = evens.map((v, i) => v + i)
	```

- ES5

	```js
	odds  = evens.map(function (v) { return v + 1; });
	pairs = evens.map(function (v) { return { even: v, odd: v + 1 }; });
	nums  = evens.map(function (v, i) { return v + i; });
	```

<h3 id="arrow-2">声明式</h3>

- ES6

	```js
	nums.forEach(v => {
	   if (v % 5 === 0)
	       fives.push(v)
	})
	```

- ES5

	```js
	nums.forEach(function (v) {
	   if (v % 5 === 0)
	       fives.push(v);
	});
	```

<h3 id="arrow-3">词法上的this</h3>
箭头函数能更直观地处理当前对象上下文。

- ES6

	```js
	this.nums.forEach((v) => {
	    if (v % 5 === 0)
	        this.fives.push(v)
	})
	```

<h2 id="param">扩展参数处理 Extended Parameter Handling</h2>
<h3 id="param-1">默认参数值</h3>
能够赋予函数参数简单、直观的默认值。

- ES6

	```js
	function f (x, y = 7, z = 42) {
	    return x + y + z
	}
	f(1) === 50
	```

- ES5

	```js
	function f (x, y, z) {
	    if (y === undefined)
	        y = 7;
	    if (z === undefined)
	        z = 42;
	    return x + y + z;
	};
	f(1) === 50;
	```

<h3 id="param-2">剩余参数</h3>
可以将可变参数函数的剩余参数整合到一个参数上。

- ES6

	```js
	function f (x, y, ...a) {
	    return (x + y) * a.length
	}
	f(1, 2, "hello", true, 7) === 9
	```

- ES5

	```js
	function f (x, y) {
	    var a = Array.prototype.slice.call(arguments, 2);
	    return (x + y) * a.length;
	};
	f(1, 2, "hello", true, 7) === 9;
	```

<h3 id="param-3">展开操作符</h3>
将可迭代集合（比如数组乃至字符串）的元素展开到字面量元素和单个函数参数中。

- ES6

	```js
	var params = [ "hello", true, 7 ]
	var other = [ 1, 2, ...params ] // [ 1, 2, "hello", true, 7 ]
	f(1, 2, ...params) === 9

	var str = "foo"
	var chars = [ ...str ] // [ "f", "o", "o" ]
	```

- ES5

	```js
	var params = [ "hello", true, 7 ];
	var other = [ 1, 2 ].concat(params); // [ 1, 2, "hello", true, 7 ]
	f.apply(undefined, [ 1, 2 ].concat(params)) === 9;

	var str = "foo";
	var chars = str.split(""); // [ "f", "o", "o" ]
	```

<h2 id="template">模板字面量 Template Literals</h2>
<h3 id="template-1">字符串插值</h3>
可以对单行和多行字符串进行直观的表达式插值。（注意：不要被名称给迷惑了，模板字面量在最初的ES6语言规范里被命名为“模板字符串”）

- ES6

	```js
	var customer = { name: "Foo" }
	var card = { amount: 7, product: "Bar", unitprice: 42 }
	message = `Hello ${customer.name},
	want to buy ${card.amount} ${card.product} for
	a total of ${card.amount * card.unitprice} bucks?`
	```

- ES5

	```js
	var customer = { name: "Foo" };
	var card = { amount: 7, product: "Bar", unitprice: 42 };
	message = "Hello " + customer.name + ",\n" +
	"want to buy " + card.amount + " " + card.product + " for\n" +
	"a total of " + (card.amount * card.unitprice) + " bucks?";
	```

<h3 id="template-2">自定义插值</h3>
可以给任意方法作灵活的表达式插值。

- ES6

	```js
	get`http://example.com/foo?bar=${bar + baz}&quux=${quux}`
	```

- ES5

	```js
	get([ "http://example.com/foo?bar=", "&quux=", "" ],bar + baz, quux);
	```

<h3 id="template-3">访问原生字符串</h3>
String.raw方法，往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。如果原字符串的斜杠已经转义，那么String.raw不会做任何处理。

- ES6

	```js
	function quux (strings, ...values) {
	    strings[0] === "foo\n"
	    strings[1] === "bar"
	    strings.raw[0] === "foo\\n"
	    strings.raw[1] === "bar"
	    values[0] === 42
	}
	quux `foo\n${ 42 }bar`

	String.raw `foo\n${ 42 }bar` === "foo\\n42bar"
	```

- ES5

	```js
	//  no equivalent in ES5
	```

<h2 id="literals">扩展字面量 Extended Literals</h2>
<h3 id="literals-1">二级制&amp;十进制字面量</h3>
直接支持安全的二进制和十进制字面量。

- ES6

	```js
	0b111110111 === 503
	0o767 === 503
	```

- ES5

	```js
	parseInt("111110111", 2) === 503;
	parseInt("767", 8) === 503;
	0767 === 503; // only in non-strict, backward compatibility mode
	```

<h3 id="literals-2">Unicode字符串&amp;正则表达式字面量</h3>
扩展字符串和正则表达式的Unicode支持。

- ES6

	```js
	"𠮷".length === 2
	"𠮷".match(/./u)[0].length === 2
	5| "𠮷" === "\uD842\uDFB7"
	"𠮷" === "\u{20BB7}"
	"𠮷".codePointAt(0) == 0x20BB7
	for (let codepoint of "𠮷") console.log(codepoint)
	```

- ES5

	```js
	"𠮷".length === 2;
	"𠮷".match(/(?:[\0-\t\x0B\f\x0E-\u2027\u202A-\uD7FF\uE000-\uFFFF][\uD800-\uDBFF][\uDC00-\uDFFF][\uD800-\uDBFF](?![\uDC00-\uDFFF])(?:[^\uD800-\uDBFF]^)[\uDC00-\uDFFF])/)[0].length === 2;
	"𠮷" === "\uD842\uDFB7";
	//  no equivalent in ES5
	//  no equivalent in ES5
	//  no equivalent in ES5
	```

<h2 id="reg">扩展正则表达式 Enhanced Regular Expression</h2>
<h3 id="reg-1">正则表达式粘连匹配</h3>
`/y`标记能保证多次匹配之间搜索位置的连贯性（即下一次匹配严格从上一次匹配的剩余字符串首字符开始），能支持任意长输入字符串的高效解析，甚至任意数量的正则表达式也能高效解析。

- ES6

	```js
	let parser = (input, match) => {
	    for (let pos = 0, lastPos = input.length; pos < lastPos; ) {
	        for (let i = 0; i < match.length; i++) {
	            match[i].pattern.lastIndex = pos
	            let found
	            if ((found = match[i].pattern.exec(input)) !== null) {
	                match[i].action(found)
	                pos = match[i].pattern.lastIndex
	                break
	            }
	        }
	    }
	}

	let report = (match) => {
	    console.log(JSON.stringify(match))
	}
	parser("Foo 1 Bar 7 Baz 42", [
	    { pattern: /^Foo\s+(\d+)/y, action: (match) => report(match) },
	    { pattern: /^Bar\s+(\d+)/y, action: (match) => report(match) },
	    { pattern: /^Baz\s+(\d+)/y, action: (match) => report(match) },
	    { pattern: /^\s*/y,         action: (match) => {}            }
	])
	```

- ES5

	```js
	var parser = function (input, match) {
	    for (var i, found, inputTmp = input; inputTmp !== ""; ) {
	        for (i = 0; i < match.length; i++) {
	            if ((found = match[i].pattern.exec(inputTmp)) !== null) {
	                match[i].action(found);
	                inputTmp = inputTmp.substr(found[0].length);
	                break;
	            }
	        }
	    }
	}

	var report = function (match) {
	    console.log(JSON.stringify(match));
	};
	parser("Foo 1 Bar 7 Baz 42", [
	    { pattern: /^Foo\s+(\d+)/, action: function (match) { report(match); } },
	    { pattern: /^Bar\s+(\d+)/, action: function (match) { report(match); } },
	    { pattern: /^Baz\s+(\d+)/, action: function (match) { report(match); } },
	    { pattern: /^\s*/,         action: function (match) {}                 }
	]);
	```

<h2 id="prop">扩展对象属性 Enhanced Object Properties</h2>
<h3 id="prop-1">属性简写</h3>
简写通用的对象属性定义语法。

- ES6

	```js
	obj = { x, y }
	```

- ES5

	```js
	obj = { x: x, y: y };
	```

<h3 id="prop-2">计算属性名称</h3>
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

<h3 id="prop-3">方法属性</h3>
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

<h2 id="destruct">解构赋值 Destructuring Assignment</h2>
<h3 id="destruct-1">数组匹配</h3>
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

<h3 id="destruct-2">简写属性匹配</h3>
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

<h3 id="destruct-3">深度属性匹配</h3>
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

<h3 id="destruct-4">上下文匹配参数</h3>
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

<h3 id="destruct-5">弱化解析失败</h3>
弱化解析失败，可以赋默认值。

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
<h2 id="modules">模块 Modules</h2>
<h3 id="modules-1">导入/导出值</h3>
模块里面的定义不会污染全局命名空间，通过从模块中导出值/向模块中导入值来实现模块之间的引用。

- ES6

	```js
	//  lib/math.js
	export function sum (x, y) { return x + y }
	export var pi = 3.141593

	//  someApp.js
	import * as math from "lib/math"
	console.log("2π = " + math.sum(math.pi, math.pi))

	//  otherApp.js
	import { sum, pi } from "lib/math"
	console.log("2π = " + sum(pi, pi))
	```

- ES5

	```js
	//  lib/math.js
	LibMath = {};
	LibMath.sum = function (x, y) { return x + y };
	LibMath.pi = 3.141593;

	//  someApp.js
	var math = LibMath;
	console.log("2π = " + math.sum(math.pi, math.pi));

	//  otherApp.js
	var sum = LibMath.sum, pi = LibMath.pi;
	console.log("2π = " + sum(pi, pi));
	```

<h3 id="modules-2">默认导出&amp;通配导出</h3>
可以将一个值作为模块的默认导出，也可以导出为值的mass-mixin。

- ES6

	```js
	//  lib/mathplusplus.js
	export * from "lib/math"
	export var e = 2.71828182846
	export default (x) => Math.exp(x)

	//  someApp.js
	import exp, { pi, e } from "lib/mathplusplus"
	console.log("e^{π} = " + exp(pi))
	```

- ES5

	```js
	//  lib/mathplusplus.js
	LibMathPP = {};
	for (symbol in LibMath)
	    if (LibMath.hasOwnProperty(symbol))
	        LibMathPP[symbol] = LibMath[symbol];
	LibMathPP.e = 2.71828182846;
	LibMathPP.exp = function (x) { return Math.exp(x) };

	//  someApp.js
	var exp = LibMathPP.exp, pi = LibMathPP.pi, e = libMathPP.e;
	console.log("e^{π} = " + exp(pi));
	```

<h2 id="class">类 Classes</h2>
<h3 id="class-1">类的定义</h3>
提供更直观的，OOP式和无原型的类定义。

- ES6

	```js
	class Shape {
	    constructor (id, x, y) {
	        this.id = id
	        this.move(x, y)
	    }
	    move (x, y) {
	        this.x = x
	        this.y = y
	    }
	}
	```

- ES5

	```js
	var Shape = function (id, x, y) {
	    this.id = id;
	    this.move(x, y);
	};
	Shape.prototype.move = function (x, y) {
	    this.x = x;
	    this.y = y;
	};
	```

<h3 id="class-2">类的继承</h3>
更直观的，OOP式和无原型的类继承。

- ES6

	```js
	class Rectangle extends Shape {
	    constructor (id, x, y, width, height) {
	        super(id, x, y)
	        this.width  = width
	        this.height = height
	    }
	}
	class Circle extends Shape {
	    constructor (id, x, y, radius) {
	        super(id, x, y)
	        this.radius = radius
	    }
	}
	```

- ES5

	```js
	var Rectangle = function (id, x, y, width, height) {
	    Shape.call(this, id, x, y);
	    this.width  = width;
	    this.height = height;
	};
	Rectangle.prototype = Object.create(Shape.prototype);
	Rectangle.prototype.constructor = Rectangle;
	var Circle = function (id, x, y, radius) {
	    Shape.call(this, id, x, y);
	    this.radius = radius;
	};
	Circle.prototype = Object.create(Shape.prototype);
	Circle.prototype.constructor = Circle;
	```

<h3 id="class-3">从表达式继承类</h3>
支持从表达式产生的函数对象扩展而成的mixin继承。（注意：当然，通常用的aggregation函数是通过一个库提供的，比如[这个](https://github.com/rse/aggregation)

- ES6

	```js
	var aggregation = (baseClass, ...mixins) => {
	    let base = class _Combined extends baseClass {
	        constructor (...args) {
	            super(...args)
	            mixins.forEach((mixin) => {
	                mixin.prototype.initializer.call(this)
	            })
	        }
	    }
	    let copyProps = (target, source) => {
	        Object.getOwnPropertyNames(source)
	            .concat(Object.getOwnPropertySymbols(source))
	            .forEach((prop) => {
	            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
	                return
	            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
	        })
	    }
	    mixins.forEach((mixin) => {
	        copyProps(base.prototype, mixin.prototype)
	        copyProps(base, mixin)
	    })
	    return base
	}

	class Colored {
	    initializer ()     { this._color = "white" }
	    get color ()       { return this._color }
	    set color (v)      { this._color = v }
	}

	class ZCoord {
	    initializer ()     { this._z = 0 }
	    get z ()           { return this._z }
	    set z (v)          { this._z = v }
	}

	class Shape {
	    constructor (x, y) { this._x = x; this._y = y }
	    get x ()           { return this._x }
	    set x (v)          { this._x = v }
	    get y ()           { return this._y }
	    set y (v)          { this._y = v }
	}

	class Rectangle extends aggregation(Shape, Colored, ZCoord) {}

	var rect = new Rectangle(7, 42)
	rect.z     = 1000
	rect.color = "red"
	console.log(rect.x, rect.y, rect.z, rect.color)
	```

- ES5

	```js
	var aggregation = function (baseClass, mixins) {
	    var base = function () {
	        baseClass.apply(this, arguments);
	        mixins.forEach(function (mixin) {
	            mixin.prototype.initializer.call(this);
	        }.bind(this));
	    };
	    base.prototype = Object.create(baseClass.prototype);
	    base.prototype.constructor = base;
	    var copyProps = function (target, source) {
	        Object.getOwnPropertyNames(source).forEach(function (prop) {
	            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
	                return
	            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
	        })
	    }
	    mixins.forEach(function (mixin) {
	        copyProps(base.prototype, mixin.prototype);
	        copyProps(base, mixin);
	    });
	    return base;
	};

	var Colored = function () {};
	Colored.prototype = {
	    initializer: function ()  { this._color = "white"; },
	    getColor:    function ()  { return this._color; },
	    setColor:    function (v) { this._color = v; }
	};

	var ZCoord = function () {};
	ZCoord.prototype = {
	    initializer: function ()  { this._z = 0; },
	    getZ:        function ()  { return this._z; },
	    setZ:        function (v) { this._z = v; }
	};

	var Shape = function (x, y) {
	    this._x = x; this._y = y;
	};
	Shape.prototype = {
	    getX: function ()  { return this._x; },
	    setX: function (v) { this._x = v; },
	    getY: function ()  { return this._y; },
	    setY: function (v) { this._y = v; }
	}

	var _Combined = aggregation(Shape, [ Colored, ZCoord ]);
	var Rectangle = function (x, y) {
	    _Combined.call(this, x, y);
	};
	Rectangle.prototype = Object.create(_Combined.prototype);
	Rectangle.prototype.constructor = Rectangle;

	var rect = new Rectangle(7, 42);
	rect.setZ(1000);
	rect.setColor("red");
	console.log(rect.getX(), rect.getY(), rect.getZ(), rect.getColor());
	```

<h3 id="class-4">基类访问</h3>
可以更直接的访问基类的构造函数和成员函数。

- ES6

	```js
	class Shape {
	    …
	    toString () {
	        return `Shape(${this.id})`
	    }
	}
	class Rectangle extends Shape {
	    constructor (id, x, y, width, height) {
	        super(id, x, y)
	        …
	    }
	    toString () {
	        return "Rectangle > " + super.toString()
	    }
	}
	class Circle extends Shape {
	    constructor (id, x, y, radius) {
	        super(id, x, y)
	        …
	    }
	    toString () {
	        return "Circle > " + super.toString()
	    }
	}
	```

- ES5

	```js
	var Shape = function (id, x, y) {
	    …
	};
	Shape.prototype.toString = function (x, y) {
	    return "Shape(" + this.id + ")"
	};
	var Rectangle = function (id, x, y, width, height) {
	    Shape.call(this, id, x, y);
	    …
	};
	Rectangle.prototype.toString = function () {
	    return "Rectangle > " + Shape.prototype.toString.call(this);
	};
	var Circle = function (id, x, y, radius) {
	    Shape.call(this, id, x, y);
	    …
	};
	Circle.prototype.toString = function () {
	    return "Circle > " + Shape.prototype.toString.call(this);
	};
	```

<h3 id="class-5">静态成员</h3>
支持声明类的静态成员。

- ES6

	```js
	class Rectangle extends Shape {
	    …
	    static defaultRectangle () {
	        return new Rectangle("default", 0, 0, 100, 100)
	    }
	}
	class Circle extends Shape {
	    …
	    static defaultCircle () {
	        return new Circle("default", 0, 0, 100)
	    }
	}
	var defRectangle = Rectangle.defaultRectangle()
	var defCircle    = Circle.defaultCircle()
	```

- ES5

	```js
	var Rectangle = function (id, x, y, width, height) {
	    …
	};
	Rectangle.defaultRectangle = function () {
	    return new Rectangle("default", 0, 0, 100, 100);
	};
	var Circle = function (id, x, y, width, height) {
	    …
	};
	Circle.defaultCircle = function () {
	    return new Circle("default", 0, 0, 100);
	};
	var defRectangle = Rectangle.defaultRectangle();
	var defCircle    = Circle.defaultCircle();
	```

<h3 id="class-6">Getter/Setter</h3>
可以直接在类里面定义Getter/Setter（不仅可以在对象初始化的时候定义，当然，从ECMAScript 5.1开始就支持了）。

- ES6

	```js
	class Rectangle {
	    constructor (width, height) {
	        this._width  = width
	        this._height = height
	    }
	    set width  (width)  { this._width = width               }
	    get width  ()       { return this._width                }
	    set height (height) { this._height = height             }
	    get height ()       { return this._height               }
	    get area   ()       { return this._width * this._height }
	}
	var r = new Rectangle(50, 20)
	r.area === 1000
	```

- ES5

	```js
	var Rectangle = function (width, height) {
	    this._width  = width;
	    this._height = height;
	};
	Rectangle.prototype = {
	    set width  (width)  { this._width = width;               },
	    get width  ()       { return this._width;                },
	    set height (height) { this._height = height;             },
	    get height ()       { return this._height;               },
	    get area   ()       { return this._width * this._height; }
	};
	var r = new Rectangle(50, 20);
	r.area === 1000;
	```

<h2 id="symbol">Symbol类型 Symbol Type</h2>
<h3 id="symbol-1">Symbol类型</h3>
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

<h3 id="symbol-2">全局Symbols</h3>
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

<h2 id="iter">迭代器 Iterators</h2>
<h3 id="iter-1">迭代器&amp;for-of操作符</h3>
允许对象通过"可迭代"的协议来自定义迭代行为；也支持通过"可迭代"协议来产生序列值（有限无限都可以）；然后提供一个简便的操作符for-of来迭代一个可迭代对象的所有值。

- ES6

	```js
	let fibonacci = {
	    [Symbol.iterator]() {
	        let pre = 0, cur = 1
	        return {
	           next () {
	               [ pre, cur ] = [ cur, pre + cur ]
	               return { done: false, value: cur }
	           }
	        }
	    }
	}

	for (let n of fibonacci) {
	    if (n > 1000)
	        break
	    console.log(n)
	}
	```

- ES5

	```js
	var fibonacci = {
	    next: (function () {
	        var pre = 0, cur = 1;
	        return function () {
	            tmp = pre;
	            pre = cur;
	            cur += tmp;
	            return cur;
	        };
	    })()
	};

	var n;
	for (;;) {
	    n = fibonacci.next();
	    if (n > 1000)
	        break;
	    console.log(n);
	}
	```

<h2 id="gen">Generators</h2>
Generator函数是ES6提供的一种异步编程解决方案，语法行为与传统函数完全不同。Generator函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象。接下来调用遍历器对象的next方法，才会使指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield语句（或return语句）为止。这样可以产生一定的序列值。

<h3 id="gen-1">使用迭代器协议的Generator函数</h3>
迭代器协议也支持Generator。

- ES6

	```js
	let fibonacci = {
	    *[Symbol.iterator]() {
	        let pre = 0, cur = 1
	        for (;;) {
	            [ pre, cur ] = [ cur, pre + cur ]
	            yield cur
	        }
	    }
	}

	for (let n of fibonacci) {
	    if (n > 1000)
	        break
	    console.log(n)
	}
	```

- ES5

	```js
	var fibonacci = {
	    next: (function () {
	        var pre = 0, cur = 1;
	        return function () {
	            tmp = pre;
	            pre = cur;
	            cur += tmp;
	            return cur;
	        };
	    })()
	};

	var n;
	for (;;) {
	    n = fibonacci.next();
	    if (n > 1000)
	        break;
	    console.log(n);
	}
	```

<h3 id="gen-2">直接使用Generator函数</h3>

- ES6

	```js
	function* range (start, end, step) {
	    while (start < end) {
	        yield start
	        start += step
	    }
	}

	for (let i of range(0, 10, 2)) {
	    console.log(i) // 0, 2, 4, 6, 8
	}
	```

- ES5

	```js
	function range (start, end, step) {
	    var list = [];
	    while (start < end) {
	        list.push(start);
	        start += step;
	    }
	    return list;
	}

	var r = range(0, 10, 2);
	for (var i = 0; i < r.length; i++) {
	    console.log(r[i]); // 0, 2, 4, 6, 8
	}
	```

<h3 id="gen-3">Generator匹配</h3>

- ES6

	```js
	let fibonacci = function* (numbers) {
	    let pre = 0, cur = 1
	    while (numbers-- > 0) {
	        [ pre, cur ] = [ cur, pre + cur ]
	        yield cur
	    }
	}

	for (let n of fibonacci(1000))
	    console.log(n)

	let numbers = [ ...fibonacci(1000) ]

	let [ n1, n2, n3, ...others ] = fibonacci(1000)
	```

- ES5

	```js
	//  no equivalent in ES5
	```

<h3 id="gen-4">Generator控制流</h3>
可以利用Generator实现"co-routines"和Promise风格的异步编程。

- ES6

	```js
	//  generic asynchronous control-flow driver
	function async (proc, ...params) {
	    var iterator = proc(...params)
	    return new Promise((resolve, reject) => {
	        let loop = (value) => {
	            let result
	            try {
	                result = iterator.next(value)
	            }
	            catch (err) {
	                reject(err)
	            }
	            if (result.done)
	                resolve(result.value)
	            else if (   typeof result.value      === "object"
	                     && typeof result.value.then === "function")
	                result.value.then((value) => {
	                    loop(value)
	                }, (err) => {
	                    reject(err)
	                })
	            else
	                loop(result.value)
	        }
	        loop()
	    })
	}

	//  application-specific asynchronous builder
	function makeAsync (text, after) {
	    return new Promise((resolve, reject) => {
	        setTimeout(() => resolve(text), after)
	    })
	}

	//  application-specific asynchronous procedure
	async(function* (greeting) {
	    let foo = yield makeAsync("foo", 300)
	    let bar = yield makeAsync("bar", 200)
	    let baz = yield makeAsync("baz", 100)
	    return `${greeting} ${foo} ${bar} ${baz}`
	}, "Hello").then((msg) => {
	    console.log("RESULT:", msg) // "Hello foo bar baz"
	})
	```

- ES5

	```js
	//  no equivalent in ES5
	```

<h3 id="gen-5">Generator方法</h3>
Generator方法可以定义在对象和类中。

- ES6

	```js
	class Clz {
	    * bar () {
	        …
	    }
	}
	let Obj = {
	    * foo () {
	        …
	    }
	}
	```

- ES5

	```js
	//  no equivalent in ES5
	```

<h2 id="set">Map/Set &amp; WeakMap/WeakSet</h2>
<h3 id="set-1">Set的数据结构</h3>
提供一个更简洁的数据结构Set。

- ES6

	```js
	let s = new Set()
	s.add("hello").add("goodbye").add("hello")
	s.size === 2
	s.has("hello") === true
	for (let key of s.values()) // insertion order
	    console.log(key)
	```

- ES5

	```js
	var s = {};
	s["hello"] = true; s["goodbye"] = true; s["hello"] = true;
	Object.keys(s).length === 2;
	s["hello"] === true;
	for (var key in s) // arbitrary order
	    if (s.hasOwnProperty(key))
	        console.log(s[key]);
	```

<h3 id="set-2">Map的数据结构</h3>
提供一个更简洁的数据结构Map。

- ES6

	```js
	let m = new Map()
	m.set("hello", 42)
	m.set(s, 34)
	m.get(s) === 34
	m.size === 2
	for (let [ key, val ] of m.entries())
	    console.log(key + " = " + val)
	```

- ES5

	```js
	var m = {};
	m["hello"] = 42;
	// no equivalent in ES5
	// no equivalent in ES5
	Object.keys(m).length === 2;
	for (key in m) {
	    if (m.hasOwnProperty(key)) {
	        var val = m[key];
	        console.log(key + " = " + val);
	    }
	}
	```

<h3 id="set-3">弱引用的数据结构</h3>
使用弱引用的WeakSet/WeakMap可以避免内存泄漏。

- ES6

	```js
	let isMarked     = new WeakSet()
	let attachedData = new WeakMap()

	export class Node {
	    constructor (id)   { this.id = id                  }
	    mark        ()     { isMarked.add(this)            }
	    unmark      ()     { isMarked.delete(this)         }
	    marked      ()     { return isMarked.has(this)     }
	    set data    (data) { attachedData.set(this, data)  }
	    get data    ()     { return attachedData.get(this) }
	}

	let foo = new Node("foo")

	JSON.stringify(foo) === '{"id":"foo"}'
	foo.mark()
	foo.data = "bar"
	foo.data === "bar"
	JSON.stringify(foo) === '{"id":"foo"}'

	isMarked.has(foo)     === true
	attachedData.has(foo) === true
	foo = null  /* remove only reference to foo */
	attachedData.has(foo) === false
	isMarked.has(foo)     === false
	```

- ES5

	```js
	// no equivalent in ES5
	```

<h2 id="array">类型数组 Typed Arrays</h2>
<h3 id="array-1">类型数组</h3>
支持任意基于字节的数据结构，更方便的实现网络协议、加密算法、文件格式操作等功能。

- ES6

	```js
	//  ES6 class equivalent to the following C structure:
	//  struct Example { unsigned long id; char username[16]; float amountDue }
	class Example {
	    constructor (buffer = new ArrayBuffer(24)) {
	        this.buffer = buffer
	    }
	    set buffer (buffer) {
	        this._buffer    = buffer
	        this._id        = new Uint32Array (this._buffer,  0,  1)
	        this._username  = new Uint8Array  (this._buffer,  4, 16)
	        this._amountDue = new Float32Array(this._buffer, 20,  1)
	    }
	    get buffer ()     { return this._buffer       }
	    set id (v)        { this._id[0] = v           }
	    get id ()         { return this._id[0]        }
	    set username (v)  { this._username[0] = v     }
	    get username ()   { return this._username[0]  }
	    set amountDue (v) { this._amountDue[0] = v    }
	    get amountDue ()  { return this._amountDue[0] }
	}

	let example = new Example()
	example.id = 7
	example.username = "John Doe"
	example.amountDue = 42.0
	```

- ES5

	```js
	//  no equivalent in ES5
	//  (only an equivalent in HTML5)
	```

<h2 id="built">新的内建方法 New Built-In Methods</h2>
<h3 id="built-1">对象属性赋值</h3>
提供一个新方法来将一个或多个源对象的可枚举属性复制到目标对象上。

- ES6

	```js
	var dst  = { quux: 0 }
	var src1 = { foo: 1, bar: 2 }
	var src2 = { foo: 3, baz: 4 }
	Object.assign(dst, src1, src2)
	dst.quux === 0
	dst.foo  === 3
	dst.bar  === 2
	dst.baz  === 4
	```

- ES5

	```js
	var dst  = { quux: 0 };
	var src1 = { foo: 1, bar: 2 };
	var src2 = { foo: 3, baz: 4 };
	Object.keys(src1).forEach(function(k) {
	    dst[k] = src1[k];
	});
	Object.keys(src2).forEach(function(e) {
	    dst[k] = src2[k];
	});
	dst.quux === 0;
	dst.foo  === 3;
	dst.bar  === 2;
	dst.baz  === 4;
	```

<h3 id="built-2">查找数组元素</h3>
提供一个新方法来查找数组里面的元素。

- ES6

	```js
	[ 1, 3, 4, 2 ].find(x => x > 3) // 4
	```

- ES5

	```js
	[ 1, 3, 4, 2 ].filter(function (x) { return x > 3; })[0]; // 4
	```

<h3 id="built-3">字符串反复</h3>
提供新的方法来反复一个字符串。

- ES6

	```js
	" ".repeat(4 * depth)
	"foo".repeat(3)
	```

- ES5

	```js
	Array((4 * depth) + 1).join(" ");
	Array(3 + 1).join("foo");
	```

<h3 id="built-4">字符串搜索</h3>
提供更多的字符串方法来搜索子串。

- ES6

	```js
	"hello".startsWith("ello", 1) // true
	"hello".endsWith("hell", 4)   // true
	"hello".includes("ell")       // true
	"hello".includes("ell", 1)    // true
	"hello".includes("ell", 2)    // false
	```

- ES5

	```js
	"hello".indexOf("ello") === 1;    // true
	"hello".indexOf("hell") === (4 - "hell".length); // true
	"hello".indexOf("ell") !== -1;    // true
	"hello".indexOf("ell", 1) !== -1; // true
	"hello".indexOf("ell", 2) !== -1; // false
	```

<h3 id="built-5">数字类型检查</h3>
提供新的方法来检查非数字和有限数字。

- ES6

	```js
	Number.isNaN(42) === false
	Number.isNaN(NaN) === true

	Number.isFinite(Infinity) === false
	Number.isFinite(-Infinity) === false
	Number.isFinite(NaN) === false
	Number.isFinite(123) === true
	```

- ES5

	```js
	var isNaN = function (n) {
	    return n !== n;
	};
	var isFinite = function (v) {
	    return (typeof v === "number" && !isNaN(v) && v !== Infinity && v !== -Infinity);
	};
	isNaN(42) === false;
	isNaN(NaN) === true;

	isFinite(Infinity) === false;
	isFinite(-Infinity) === false;
	isFinite(NaN) === false;
	isFinite(123) === true;
	```

<h3 id="built-6">数字安全检查</h3>
提供新的方法来检查一个整数是不是在安全范围内，即是不是能被JavaScript正确表示（因为JavaScript的所有数字，包括整数，技术上来说都是浮点型）。

- ES6

	```js
	Number.isSafeInteger(42) === true
	Number.isSafeInteger(9007199254740992) === false
	```

- ES5

	```js
	function isSafeInteger (n) {
	    return (
	           typeof n === 'number'
	        && Math.round(n) === n
	        && -(Math.pow(2, 53) - 1) <= n
	        && n <= (Math.pow(2, 53) - 1)
	    );
	}
	isSafeInteger(42) === true;
	isSafeInteger(9007199254740992) === false;
	```

<h3 id="built-7">数字比较</h3>
提供一个标准值ε来实现更精确的浮点型数字比较。

- ES6

	```js
	console.log(0.1 + 0.2 === 0.3) // false
	console.log(Math.abs((0.1 + 0.2) - 0.3) < Number.EPSILON) // true
	```

- ES5

	```js
	console.log(0.1 + 0.2 === 0.3); // false
	console.log(Math.abs((0.1 + 0.2) - 0.3) < 2.220446049250313e-16); // true
	```

<h3 id="built-8">数字截断</h3>
提供一个新方法来截断数字的整数部分，完全抛弃其小数部分。

- ES6

	```js
	console.log(Math.trunc(42.7)) // 42
	console.log(Math.trunc( 0.1)) // 0
	console.log(Math.trunc(-0.1)) // -0
	```

- ES5

	```js
	function mathTrunc (x) {
	    return (x < 0 ? Math.ceil(x) : Math.floor(x));
	}
	console.log(mathTrunc(42.7)) // 42
	console.log(mathTrunc( 0.1)) // 0
	console.log(mathTrunc(-0.1)) // -0
	```

<h3 id="built-9">确定数字符号</h3>
提供新的方法来确定数字的符号，包括带符号的0和非数字等特殊情况。

- ES6

	```js
	console.log(Math.sign(7))   // 1
	console.log(Math.sign(0))   // 0
	console.log(Math.sign(-0))  // -0
	console.log(Math.sign(-7))  // -1
	console.log(Math.sign(NaN)) // NaN
	```

- ES5

	```js
	function mathSign (x) {
	    return ((x === 0 || isNaN(x)) ? x : (x > 0 ? 1 : -1));
	}
	console.log(mathSign(7))   // 1
	console.log(mathSign(0))   // 0
	console.log(mathSign(-0))  // -0
	console.log(mathSign(-7))  // -1
	console.log(mathSign(NaN)) // NaN
	```

<h2 id="promises">异步编程Promises</h2>
<h3 id="promises-1">Promise的用法</h3>
Promise是异步编程的一种方案，ES6提供了Promise类来方便的实现异步编程功能。如果对Promise编程原理不了解可以Google。

- ES6

	```js
	function msgAfterTimeout (msg, who, timeout) {
	    return new Promise((resolve, reject) => {
	        setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
	    })
	}
	msgAfterTimeout("", "Foo", 100).then((msg) =>
	    msgAfterTimeout(msg, "Bar", 200)
	).then((msg) => {
	    console.log(`done after 300ms:${msg}`)
	})
	```

- ES5

	```js
	function msgAfterTimeout (msg, who, timeout, onDone) {
	    setTimeout(function () {
	        onDone(msg + " Hello " + who + "!");
	    }, timeout);
	}
	msgAfterTimeout("", "Foo", 100, function (msg) {
	    msgAfterTimeout(msg, "Bar", 200, function (msg) {
	        console.log("done after 300ms:" + msg);
	    });
	});
	```

<h3 id="promises-2">Promise的结合</h3>
提供一个接口来整合多个Promise的执行，使你不用去管理底层的异步操作顺序。

- ES6

	```js
	function fetchAsync (url, timeout, onData, onError) {
	    …
	}
	let fetchPromised = (url, timeout) => {
	    return new Promise((resolve, reject) => {
	        fetchAsync(url, timeout, resolve, reject)
	    })
	}
	Promise.all([
	    fetchPromised("http://backend/foo.txt", 500),
	    fetchPromised("http://backend/bar.txt", 500),
	    fetchPromised("http://backend/baz.txt", 500)
	]).then((data) => {
	    let [ foo, bar, baz ] = data
	    console.log(`success: foo=${foo} bar=${bar} baz=${baz}`)
	}, (err) => {
	    console.log(`error: ${err}`)
	})
	```

- ES5

	```js
	function fetchAsync (url, timeout, onData, onError) {
	    …
	}
	function fetchAll (request, onData, onError) {
	    var result = [], results = 0;
	    for (var i = 0; i < request.length; i++) {
	        result[i] = null;
	        (function (i) {
	            fetchAsync(request[i].url, request[i].timeout, function (data) {
	                result[i] = data;
	                if (++results === request.length)
	                    onData(result);
	            }, onError);
	        })(i);
	    }
	}
	fetchAll([
	    { url: "http://backend/foo.txt", timeout: 500 },
	    { url: "http://backend/bar.txt", timeout: 500 },
	    { url: "http://backend/baz.txt", timeout: 500 }
	], function (data) {
	    var foo = data[0], bar = data[1], baz = data[2];
	    console.log("success: foo=" + foo + " bar=" + bar + " baz=" + baz);
	}, function (err) {
	    console.log("error: " + err);
	});
	```

<h2 id="meta">元编程 Meta-Programming</h2>
<h3 id="meta-1">Proxy</h3>
Proxy用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。Proxy可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

- ES6

	```js
	let target = {
	    foo: "Welcome, foo"
	}
	let proxy = new Proxy(target, {
	    get (receiver, name) {
	        return name in receiver ? receiver[name] : `Hello, ${name}`
	    }
	})
	proxy.foo   === "Welcome, foo"
	proxy.world === "Hello, world"
	```

- ES5

	```js
	// no equivalent in ES5
	```

<h3 id="meta-2">Reflection</h3>
Reflect对象也是ES6为了操作对象而提供的新API，目的是将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上；让Object操作都变成函数行为；Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法，不管Proxy怎么修改默认行为，你总可以在Reflect上获取默认行为。

- ES6

	```js
	let obj = { a: 1 }
	Object.defineProperty(obj, "b", { value: 2 })
	obj[Symbol("c")] = 3
	Reflect.ownKeys(obj) // [ "a", "b", Symbol(c) ]
	```

- ES5

	```js
	var obj = { a: 1 };
	Object.defineProperty(obj, "b", { value: 2 });
	// no equivalent in ES5
	Object.getOwnPropertyNames(obj); // [ "a", "b" ]
	```


<h2 id="inter">国际化及本地化 Meta-Programming</h2>
<h3 id="inter-1">校对</h3>
对于字符串集合的排序和检索，可以根据地区和Unicode为参数进行校对。

- ES6

	```js
	// in German,  "ä" sorts with "a"
	// in Swedish, "ä" sorts after "z"
	var list = [ "ä", "a", "z" ]
	var i10nDE = new Intl.Collator("de")
	var i10nSV = new Intl.Collator("sv")
	i10nDE.compare("ä", "z") === -1
	i10nSV.compare("ä", "z") === +1
	console.log(list.sort(i10nDE.compare)) // [ "a", "ä", "z" ]
	console.log(list.sort(i10nSV.compare)) // [ "a", "z", "ä" ]
	```

- ES5

	```js
	// no equivalent in ES5
	```

<h3 id="inter-2">数字格式化</h3>
可以根据数字分组和本地的分隔符，对数字进行格式化。

- ES6

	```js
	var i10nEN = new Intl.NumberFormat("en-US")
	var i10nDE = new Intl.NumberFormat("de-DE")
	i10nEN.format(1234567.89) === "1,234,567.89"
	i10nDE.format(1234567.89) === "1.234.567,89"
	```

- ES5

	```js
	// no equivalent in ES5
	```

<h3 id="inter-3">货币格式化</h3>
可以根据数字分组、本地的分隔符和附加的货币符号，对数字进行格式化。

- ES6

	```js
	var i10nUSD = new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" })
	var i10nGBP = new Intl.NumberFormat("en-GB", { style: "currency", currency: "GBP" })
	var i10nEUR = new Intl.NumberFormat("de-DE", { style: "currency", currency: "EUR" })
	i10nUSD.format(100200300.40) === "$100,200,300.40"
	i10nGBP.format(100200300.40) === "£100,200,300.40"
	i10nEUR.format(100200300.40) === "100.200.300,40 €"
	```

- ES5

	```js
	// no equivalent in ES5
	```

<h3 id="inter-4">日期/时间格式化</h3>
可以根据本地的顺序和分隔符格式化时间和日期。

- ES6

	```js
	var i10nEN = new Intl.DateTimeFormat("en-US")
	var i10nDE = new Intl.DateTimeFormat("de-DE")
	i10nEN.format(new Date("2015-01-02")) === "1/2/2015"
	i10nDE.format(new Date("2015-01-02")) === "2.1.2015"
	```

- ES5

	```js
	// no equivalent in ES5
	```

