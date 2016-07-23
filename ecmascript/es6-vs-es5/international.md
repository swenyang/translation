# 国际化及本地化 Meta-Programming

## 校对

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

## 数字格式化

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

## 货币格式化

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

## 日期/时间格式化

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

