title: javascript中的函数作用域
date: 2015-09-01 17:22:26
tags: [Javascript]
---
在javascript中函数和变量的声明都会被提升，然而函数和变量的提升中也包含优先关系，并且提升过程是函数优先；看下面的代码：

``` javascript
foo(); //'a'

var foo;
function foo()
{
	console.log('a');
}
foo = function()
{
	console.log('b');
} 
```
<!-- more -->
上面的代码在编译时被理解为：
``` javascript
function foo()
{
	console.log('a');
}

foo(); //'a'

foo = function()
{
	console.log('b');
} 
```
上面的代码中一开始便调用了foo(),变量foo的声明出现在function foo(){}声明之前，如果变量的提升优先于函数.则foo()调用时应该引用的是下面赋给foo 的那个匿名函数，输出的是b，而运行的结果并非如此，因此提升的过程是函数优先。

这样再来看代码运行的结果就很容易理解了，对比编译前后的代码，会发现编译后var foo没有了，这是因为编译器将重复的声明忽略了。

``` javascript
foo(); //'a'

var foo;
function foo()
{
	console.log('a');
}
foo = 'b';
foo(); //TypeError: foo is not a function
console.log(foo.toString()); // 'b'
```
上面的代码中再次调用foo()报错的原因是函数foo()被赋了新值字符'b'， 这里的foo依然是开始时声明的foo函数，也就是说当代码中包含重复的声明时，会被编译器忽略，如果存在新的赋值或函数引用，则会引用新赋值或函数引用。
