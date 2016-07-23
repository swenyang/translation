
# 扩展字面量 Extended Literals

## 二级制&十进制字面量

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

## Unicode字符串&正则表达式字面量

支持扩展字符串和正则表达式的Unicode。

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