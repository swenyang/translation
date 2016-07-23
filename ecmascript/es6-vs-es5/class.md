# 类 Classes

## 类的定义

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

## 类的继承

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

## 从表达式继承类

支持从表达式产生的函数对象扩展而成的mixin继承。（注意：当然，通常用的aggregation函数是通过一个库提供的，比如[这个](https://github.com/rse/aggregation)）

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

## 基类访问

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

## 静态成员

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

## Getter/Setter

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