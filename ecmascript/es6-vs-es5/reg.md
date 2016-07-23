# 扩展正则表达式 Enhanced Regular Expression

## 正则表达式粘连匹配

`/y`标记能保证多次匹配之间搜索位置的连贯性（即下一次匹配严格从上一次匹配的剩余字符串首字符开始），能支持任意长输入字符串的高效解析，甚至是任意数量的正则表达式。

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