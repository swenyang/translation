# 元编程 Meta-Programming

## Proxy

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

## Reflection

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
