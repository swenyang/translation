# 迭代器 Iterators

## 迭代器&amp;for-of操作符

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
