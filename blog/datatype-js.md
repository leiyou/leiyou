title: Javascript中的数据类型
date: 2015-07-01
tags: [Javascript]
---
javascript属于动态类型的语言，当我们在js中定义一个变量时，并不用声明变量的类型，这一点与强语言（如C，C++）不同。js的解释器会根据变量的字面量值，自动的识别出变量的数据类型，js中基本的数据类型有五种，分别是Number、String、Boolean、Null，undefined，还有一种抽象数据类型——Object。

一、Number表示数值类型，整数和浮点型数据都属于Number类型，同时Number可以表示二进制、八进制、十六进制的数。ECMAScript能表示的最小和最大数值保存在Number.MIN_VALUE 和 Number.MAX_VALUE 中，在chrome和Firefox中测试的这俩值为5e-324和1.7976931348623157e+308;若某个数值超出了js数值范围，则会将该数值自动转为i特殊的Infinity值，分为正无穷和负无穷。判断一个数值是否为Infinity值，可用函数isFinity()。

数值转换函数parseInt(),parseFloat(),具体用法：

``` js
console.log(parseInt("123abc")); //123
console.log(parseInt("0xA"));	//10 十六进制数
console.log(parseInt(""));	//NaN
console.log(parseInt('10', 2));	//1010 第二个参数指定输出数进制数
```
``` js
console.log(parseFloat("123abc")); //123
console.log(parseFloat('1.22'));  //1.22 
console.log(parseFloat('0xA')); //0
```
 
二、String表示字符串，可由单引号或双引号表示，获取字符串的长度可通过length属性，在解析转义字符时，会将其作为一个字符来解析。

如下中的“\u03a3”被解析为一个字符：
``` js
var text = "this is the letter sigma: \u03a3."
console.log(text.length);//28	
```

将数值转为String，可用toString()方法，同时还可以在该方法中添加一个参数，指定输出数值的基数;
如：
``` js
var num = 10;
console.log(num.toString(2));	//"1010"
```
String()方法可以将任何类型的数值转为字符串，如：

``` js
var age = null;
var name;

console.log(String(age));	//"null"
console.log(String(name));	//"undefined"
```
<!-- more -->
三、Null，字意上理解为空，通常用于初始化对象类型变量，用typeof检测其类型

``` js
console.log(typeof null); // object
```
四、Undefined，通常在仅声明变量，而未赋值时的情况下，则该变量会被自动赋值为undefined，undefined派生于null，如：

``` js
var num;
console.log(num);	//undefined	
console.log(null == undefined); //true
```
五、Boolean类型值常用于条件判断语句中，对于string、object、非零数字都可以转换为true的布尔值，而NaN、undefined，null则对应false;
 
六、Object是js中重要的数据类型，在面向对象编程中对象的概念是必不可少的，通过创建object类型，可以创建一个对象，有了对象便可以在其中添加属性和方法。当一个对象调用toString()时，返回的是该对象的字符串表示;

