# Markdown入门教程
本文摘要自<http://wowubuntu.com/markdown/index.html>，增删了部分内容。

# 简介
- 宗旨

	HTML文件的标记，格式书写起来非常繁琐，Markdown大大简化了书写语法，让使用者更关注于写作内容。简单地说，Markdown是一种书写格式，HTML则是一种发布格式。

- 兼容HTML

	不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写。
		这是一个普通段落。

		<table>
		    <tr>
		        <td>Foo</td>
		    </tr>
		</table>

		这是另一个普通段落。

- 特殊字符自动转换

	`&`和`<`不用特殊处理成`&amp;`和`&lt;`了，比如可以直接写

		AT&T

# 区块元素
- 段落和换行

	段落前后要有一个以上的空行；只包含空格和制表符的也被视为空行；普通段落不该用空格或制表符缩进；Markdown不会为每个换行符生成`<br/>`

- 标题
	- 类Setext形式

		用底线的形式，利用`=`（最高阶标题）和`-`（第二阶标题）

			This is an H1
			=============
			This is an H2
			-------------

	- 类atx形式

		在行首插入1到6个`#`，对应到标题1到6阶

			# 这是 H1
			## 这是 H2
			###### 这是 H6

- 区块引用Blockquotes

	在每行前面加`>`，也可以只在整个段落的第一行加上>；区块引用可以嵌套，根据层次加上不同数量的`>`即可；引用的区块内也可以使用其他的 Markdown 语法，包括标题、列表、代码区块等。

		> ## 这是一个标题。
		>
		> 1.   这是第一行列表项。
		> 2.   这是第二行列表项。
		>
		> 给出一些例子代码：
		>
		>     return shell_exec("echo $input | $markdown_script");

- 列表
	- 无序列表

		无序列表使用星号`*`、加号`+`或是减号`-`作为列表标记

			*   Red
			+   Green
			-  Blue

	- 有序列表

		有序列表则使用数字接着一个英文句点，使用的数字不影响输出结果，但最好还是从1开始

			1.  Bird
			2.  McHale
			3.  Parish

		如果列表项目间用空行分开，在输出HTML时Markdown就会将项目内容用`<p>`标签包起来。
		列表项目可以包含多个段落，每个项目下的段落都必须缩进 4 个空格或是 1 个制表符。
		如果要放代码区块的话，该区块就需要缩进两次，也就是 8 个空格或是 2 个制表符：

			* 一列表项包含一个列表区块：

					<代码写在这>

		在行首出现数字-句点-空白，要避免这样的状况，你可以在句点前面加上反斜杠。

			1986\. What a great season.

- 代码区块

	建立代码区块只要简单地缩进 4 个空格或是 1 个制表符就可以

		Here is an example of AppleScript:

		    tell application "Foo"
		        beep
		    end tell

	在代码区块里面，`&`、`<`和`>`会自动转成 HTML 实体；代码区块中，一般的 Markdown 语法不会被转换，像是星号便只是星号。
另一种方式是用两个` ``` `包裹起来，可以声明代码类型，实现语法高亮：

		```js
		var hello = "world";
		console.log(hello);
		```

- 分隔线

	在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西。你也可以在星号或是减号中间插入空格。下面每种写法都可以建立分隔线：

		* * *

		***

		*****

		- - -

		---------------------------------------

# 区段元素
- 链接
	- 行内式

		要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可

			This is [an example](http://example.com/ "Title") inline link.
			[This link](http://example.net/) has no title attribute.
		如果你是要链接到同样主机的资源，你可以使用相对路径：
			See my [About](/about/) page for details.

	- 参考式

		参考式的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记：

			This is [an example][id] reference-style link.

		接着，在文件的任意处，你可以把这个标记的链接内容定义出来，链接辨别标签可以有字母、数字、空白和标点符号，但是并不区分大小写：

			[id]: http://example.com/  "Optional Title Here"

		隐式链接标记功能可以省略指定链接标记，此时，链接标记会视为等同于链接文字，要用隐式链接标记只要在链接文字后面加上一个空的方括号，如果你要让 "Google" 链接到 google.com，你可以简化成

			[Google][]
			[Google]: http://google.com/

- 强调

	Markdown 使用星号`*`和底线`_`作为标记强调字词的符号，被`*`或`_`包围的字词会被转成用`<em>`标签包围，用两个`*`或`_`包起来的话，则会被转成`<strong>`，例如：

			*single asterisks*
			_single underscores_
			**double asterisks**
			__double underscores__

	但是如果你的`*`和`_`两边都有空白的话，它们就只会被当成普通的符号。

- 代码

	如果要标记一小段行内代码，你可以用反引号把它包起来`` ` ``，例如：

		Use the `printf()` function.

	如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段：

		A single backtick in a code span: `` ` ``
		A backtick-delimited string in a code span: `` `foo` ``

	代码区段的起始和结束端都可以放入一个空白，起始端后面一个，结束端前面一个，这样你就可以在区段的一开始就插入反引号：

		A single backtick in a code span: `` ` ``
		A backtick-delimited string in a code span: `` `foo` ``

- 图片

	和链接很相似，同样用行内式和参考式。到目前为止，Markdown还没有办法指定图片的宽高，如果你需要的话，你可以使用普通的`<img>`标签。
	- 行内式

			![Alt text](/path/to/img.jpg "Optional title")

	- 参考式

			![Alt text][id]
			[id]: url/to/image  "Optional title attribute"

# 其他
- 自动链接

	Markdown 支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用尖括号包起来， Markdown 就会自动把它转成链接。一般网址的链接文字就和链接地址一样，例如：

		<http://example.com/>

	邮址的自动链接也很类似，只是Markdown会先做一个编码转换的过程，把文字字符转成 16 进位码的 HTML 实体，这样的格式可以糊弄一些不好的邮址收集机器人

		<address@example.com>

- 反斜杠

	Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：

		\   反斜线
		`   反引号
		*   星号
		_   底线
		{}  花括号
		[]  方括号
		()  括弧
		#   井字号
		+   加号
		-   减号
		.   英文句点
		!   惊叹号
