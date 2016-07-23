# Map/Set &amp; WeakMap/WeakSet

## Set的数据结构

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

## Map的数据结构

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

## 弱引用的数据结构

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