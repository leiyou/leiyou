title: 重识javascript中的函数表达式
date: 2015-10-26 22:51:53
tags: [Javascript]
---
在<a href="http://segmentfault.com/q/1010000003872849">segmentfault</a>上看到一个问答，问题是这样的：
``` js
b = c;

b();
console.log(a);    //1
console.log(b);    //2
console.log(c);    //3

function c() {
    a = 1, b = 2, c = 3;
}
```
将上面的代码稍微修改为：
``` js
b = function c() {
    a = 1, b = 2, c = 3;
};

b();
console.log(a);    //1
console.log(b);    //2
console.log(c);    //Uncaught ReferenceError: c is not defined
```
<!-- more -->
再次将代码修改为：
``` js
b = function c() {
    a = 1, b = 2, c = 3;
    console.log(a);    //1
    console.log(b);    //2
    console.log(c);    //fuction c(){...
};
b();
```
咋一看，这段代码应该是和javascript中的<strong>函数提升</strong>有关；关于这点，javascript中有一个
称为<strong>提升</strong>的概念，可以理解为变量和函数声明从它们在代码中出现的位置移动到了程序最上面．
来段代码：
``` js
console.log(a); // undefine
var a = 1;
```
这段代码中声明了变量ａ，同时将１赋值给它；运行这段代码输出的是undefine,这说明变量ａ是已经声明了的，但还没有被赋值，故输出undefine;上面的代码等价于：
``` js
var a;
console.log(a); // undefine
a = 1;
```
显然变量<code>a</code>的声明<code>var a;</code>被移动到了最上面，这个过程就是<strong>变量的提升</strong>．同理我们可以看看函数声明的提升：
``` js
foo(); // foo function.

function foo() {
	console.log('foo function.')
}
```
与其等价的代码：
``` js
function foo() {
	console.log('foo function.')
}

foo(); // foo function.
```
既然函数声明会被提升，那么函数表达式是否也会被提升呢？
``` js
foo(); // ReferenceError: foo is not defined

var fun = function foo() {
	console.log('foo function.')
}

fun();
foo(); // ReferenceError: foo is not defined
```
从上面代码的运行结果可知，函数foo没有被提升，而且从输出来看foo()压根不存在．通过上面的几个简单的例子,我们可以知道变量和函数的声明会被提升到其作用域的最上面，而且提升仅是针对声明，赋值或是其他程序会留在原地．比如上面的例子:
``` js
var a;
console.log(a); // undefine
a = 1;
```
赋值语句并没有被移动，仍然是在原来的位置．若是在函数中声明变量，则变量声明会被提升到函数内部的最顶端．如
``` js
function fun() {
	console.log(b); //undefine
	var b = 1;
}
//等价于：
function fun() {
	var b;
	console.log(b); //undefine
	b = 1;
}
```
说了那么多，现在再来看看文章开头处提到的那个问题．先看第一段代码：
``` js
b = c;

b();
console.log(a);    //1
console.log(b);    //2
console.log(c);    //3

function c() {
    a = 1, b = 2, c = 3;
}
```
这段代码中有函数声明，根据前面说的函数提升，因此代码在编译时实际上是这样：
``` js
function c() {
    a = 1, b = 2, c = 3;
}
b = c;

b();
console.log(a);    //1
console.log(b);    //2
console.log(c);    //3
```
这样理解起来就很自然了，函数<code>c()</code>声明后赋值给了变量<code>b</code>,之后执行<code>b()</code>函数，此时变量<code>b, c</code>被重新赋值，即<pre>b = 2, c = 3</pre>
也就是说原来的函数<code>b(), c()</code>已经变成了数值类型，如果再次调用<code>b(),c()</code>函数，则会输出类型错误．
说完了第一段代码，再来看它的第二个版本：
``` js
b = function c() {
    a = 1, b = 2, c = 3;
};

b();
console.log(a);    //1
console.log(b);    //2
console.log(c);    //Uncaught ReferenceError: c is not defined
```
与第一段代码相比，明显的区别是这段代码中没有了函数声明，取而代之的是函数表达式．前面说过函数声明会被提升，而函数表达式不会，因此在函数表达式外，c()函数是不存在的．既然函数体外访问不了c,那么在函数体内直接对它重新赋值呢？看下面的几张截图就知道答案啦．

<img src="/images/study/2015-10-26-01.png" alt="image1">

<img src="/images/study/2015-10-26-02.png" alt="image2">

<img src="/images/study/2015-10-26-03.png" alt="image3">


如果真的想要重新覆盖掉标识符<code>c</code>呢．经过测试重新声明一个ｃ变量就可以了．比如：

<img src="/images/study/2015-10-26-04.png" alt="image4">


关于函数表达式，ＥＣＭＡ规范中声明，在函数表达式中，标识符也就是函数名在只能在函数体内有效，而对于函数体外是不可见的．

最后对于第三段代码，其实明白了第二段代码的话，这段代码也就很好理解了．这里就不再唠叨了，原理和第二段的是相同的．
``` js
b = function c() {
    a = 1, b = 2, c = 3;
    console.log(a);    //1
    console.log(b);    //2
    console.log(c);    //fuction c(){...
};
b();
```
<hr>
总结：
（１）javascript中变量或函数的声明会被提升到当前作用域的顶部，同时函数提升优先于变量．
（２）变量或是函数的提升仅仅是对声明部分移动位置，原有的语句（如赋值，逻辑结构）位置不变．
（３）函数表达式不会被提升，而且其标识符（函数名）仅在自身函数体内有效，在函数体为不可见．
