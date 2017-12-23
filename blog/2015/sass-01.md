title: Sass学习笔记(一)
date: 2015-10-13 16:44:57
tags: [Sass, CSS]
---

Sass 是css的扩展，使用ｓａｓｓ可以更快，更方便的书写和管理ｃｓｓ文件；ｓａｓｓ文件有两种文件格式ｓｃｓｓ和ｓａｓｓ，两者在语法上有些差别，比如在ｓｃｓｓ文件中以括号来区分块，而ｓａｓｓ中则是通过缩进来区分．ｓａｓｓ之所以在前端流行，与它所具有的特性有关，如变量，嵌套，混合等等特性，可以使得ｃｓｓ看起来更像一门编程语言．

#### 安装
Ｓａｓｓ是使用ｒｕｂｙ写的，所以安装前须先安装ｒｕｂｙ环境,然后再通过ｇｅｍ安装ｓａｓｓ
```shell
gem install sass
```
#### 使用

##### 编译ｓａｓｓ文件
``` shell
sass main.scss main.css
```
<!-- more -->
编译后输出的ｃｓｓ文件有四种风格，nested (default), compact, compressed, or expanded. 
我比较喜欢expanded（扩展）风格，感觉易读性比较好，当然实际项目中ｃｓｓ文件基本采用压缩（compressed）后的格式．添加- -style命令选项即可改变ｃｓｓ的输出格式．
``` shell
sass --style expanded main.scss main.css
```
编写sass文件时，当文件改变时可以通过－－ｗａｔｃｈ命令同步修改到输出文件中.
如监听ｍａｉｎ.scss文件，并将文件输出到ｍａｉｎ.css
``` shell
sass --watch main.scss:main.css
```
监听某个目录下的ｓａｓｓ文件：
``` shell
sass --watch sass/:app/style
```

sass 命令可以将ｓｃｓｓ和ｓａｓｓ格式的文件互相转换．
``` shell
sass-convert main.scss main.sass
```
sass还包含其他的命令，　可以使用<code>sass --help</code>查看具体的使用方法．

##### 变量
ｓａｓｓ中的变量可以表示的数据类型有<code>numbers, strings, colors, booleans, null, lists, map</code>．如<code>１，２，16px</code>都可以表示数值，<code>"foo", bar</code>表示字符，<code>true, false</code>表示布尔值，<code>null</code>表示空(null)，	<code>16px Arial normal</code>表示列表值， <code>key: value</code>表示散列表．

``` sass
$width: 960px;

.main{
	width: $width;
}
```
如上所示，ｓａｓｓ中定义变量是通过$符号开始，变量名可由字母，数字，下划线组成，但要注意$符后的第一个字符应当为字母或下划线，如合法的命名：<code>$width, $_width, $_960width</code>, 非法的命名：　<code>$960width</code>

在字符串中引用变量的方式：
``` sass
$top: top;

.box {
	margin-{$top}: 5px;
}
```
##### 嵌套
``` sass
header{
	h1{
		text-align: center;
	}
}
```
等价于
``` sass
header h1{
	text-align: center;
}
```
在sass中<code>＆符</code>用于表示其父元素，如
``` sass
a {
	&:hover {
		background-color: #FFF;	
	}
}
```
等价于
``` sass
a:hover {
	background-color: #FFF;	
}
```
其实这种方式等价与ｃｓｓ中的后代选择器，当然使用ｓａｓｓ的嵌套可以节省一定的代码．

##### 混合(@mixin)
混合类似于一个函数，可以指定参数设定特定的属性值，定义一个混合：
``` sass
@mixin transform($value, $originX, $originY) {
 	-webkit-transform-origin: $originX $originY;
	-moz-transform-origin: $originX $originY; 
	-ms-transform-origin: $originX $originY;
	-o-transform-origin: $originX $originY;	
 	transform-origin: $originX $originY;
 	-webkit-transform: scale($value);
 	-moz-transform: scale($value);
 	-ms-transform: scale($value);
 	-o-transform: scale($value);
 	transform: scale($value);
 }
```
使用<code>＠include</code>引用混合：
``` sass
.toolbar-app {
	opacity: 1;
	-ms-filter: 'progid: DXImageTransform.Microsoft.Alpha(Opacity=100)';
	@include transform(1, 95%, 95%);
}
```

##### 继承
``` sass
.fixed {
	position: fixed;
}

.header {
	@extend .fixed;
	width: 100%;
	height: 80px;
}
```
通过继承可以将类.fix中的代码包含到类.header中．通常在写样式文件时，会有重复的代码，这时可以使用继承属性将公共的部分提取出来，之后若要包含到某个类中，则通过<code>@extend</code>应用就可以了．

##### 运算符
``` sass
p {
  font: 10px/8px;             // Plain CSS, no division
  $width: 1000px;
  width: $width/2;            // Uses a variable, does division
  width: round(1.5)/2;        // Uses a function, does division
  height: (500px/2);          // Uses parentheses, does division
  margin-left: 5px + 8px/2px; // Uses +, does division
  font: (italic bold 10px/8px); // In a list, parentheses don't count
}
```
等价于
``` sass
p {
  font: 10px/8px;
  width: 500px;
  height: 250px;
  margin-left: 9px;
}
```
从上面可以看出<code>/</code>在含有运算符(＋，　－，　＊，　括号)的情况下才解析为除法运算符