# 箭头函数 Arrow Functions

## 表达式

箭头函数类似于匿名函数，但是箭头函数表达能力更强，也支持闭包。

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

## 声明式

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

## 词法上的this

箭头函数能更直观地处理当前对象上下文（箭头函数内部的this保持为声明时的this）。

- ES6

	```js
	this.nums.forEach((v) => {
	    if (v % 5 === 0)
	        this.fives.push(v)
	})
	```
