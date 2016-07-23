# 新的内建方法 New Built-In Methods

## 对象属性赋值

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

## 查找数组元素

提供一个新方法来查找数组里面的元素。

- ES6

	```js
	[ 1, 3, 4, 2 ].find(x => x > 3) // 4
	```


- ES5

	```js
	[ 1, 3, 4, 2 ].filter(function (x) { return x > 3; })[0]; // 4
	```

## 字符串反复

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

## 字符串搜索

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

## 数字类型检查

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

## 数字安全检查

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

## 数字比较

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

## 数字截断

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

## 确定数字符号

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