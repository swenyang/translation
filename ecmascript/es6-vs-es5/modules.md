# 模块 Modules

## 导入/导出值

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

## 默认导出&amp;通配导出

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
