---
title: JavaScript Array中的原型方法
date: 2017-09-01 13:46:00
tags: Javascript
---


## 创建数组

``` js
var a = [1, 2, 3];         # 字面量方式创建
var b = new Array(1, 2, 3) # 构造函数的方式创建，可省略new关键字
```
*创建数组时有趣的事：*

- 数组长度不需要指定；
- 当使用Array构造函数时，若只有一个参数则该参数作为数组长度；
- 数组的长度由下标索引决定，如：
``` js
var a = [1, 2, 3];
a [13] = 13;

console.log(a.length) // 14
```
- 稀疏数组，即存在很多空白或空缺单元的数组；
- 数组也是对象，因此也可以通过key-value的方式进行索引：
``` js
var a = [];
a[0] = 1;
a['type'] = 'js'

console.log(a.length); # 1

a['13'] = 'AI';

console.log（a.length); # 14
```
*注意： 通过key-value方式添加的元素不计入数组的长度, 如果字符可以转换为数值则会计入length;*

- 清空数组的方法：
- 重置数组长度为 0；

## 数组方法

1、indexOf(arg, fromindex): 查找与arg匹配的元素，fromindex指定查找的起始位置, 若省略默认从首字符开始查找.并返回元素下标；若无匹配项则返回-1；

``` js
var a = [1, 2, 3, 'java'];
console.log(a.indexOf(3));     # 2
console.log(a.indexOf('ja'));  # -1
cosole.log(a.indexOf('java')); # 3
```
* 与lastIndexOf()则是从后往前查找.

2、 concat(arr): 数组连接，将arr数组中的元素追加到另一数组中，执行后的操作不会保留在数组；
``` js
var a = [1, 2, 3];
var b = ['fly', 'with', 'me'];

console.log(a.concat(b)); # [1, 2, 3, 'fly', 'with', 'me']
console.log(a);           # [1, 2, 3]
```

3、 push(value): 向数组中添加一个元素, 即入栈操作, 返回入栈后的数组长度.(栈的出入栈规则是后入先出)
``` js
var a = [1, 2];
var count = a.push(3);  

console.log(count); # 3
console.log(a); # [1, 2, 3]
```

4、 pop(): 弹出栈顶元素, 即出栈操作, 返回值为栈顶元素.
``` js
var a = [];
var count = a.push(1, 2, 3);  
var top = a.pop(); 

console.log(top); # 3
console.log(a); # [1, 2]
```

5、 shift(): 与pop() 方法相反, 返回的是栈底元素.
``` js
var a = [];
var count = a.push(1, 2, 3);  
var bottom = a.shift();  

console.log(bottom); # 1
console.log(a); # [2, 3]
```

6、 unshift(value): 入栈操作, 与push不同的是unshift()将元素压入栈底, 返回值为入栈后数组的长度.
``` js
var a = [1, 2];
var count = a.unshift(3);

console.log(count); # 3
console.log(a);     # [3, 1, 2]
```

7、 reverse(): 数组反转函数, 执行后首尾元素互换, 返回值为反转后的数组元素.
``` js
var a = [1, 2, 3];
var b = a.reverse();

console.log(a); # [3, 2, 1]
console.log(b); # [3, 2, 1]
```

8、join(symbol): 将数组以symbol为分隔符转为字符串, 执行该方法不会改变原有数组.
``` js
var a = ['l', 'e', 'i'];
var b = a.join('');

console.log(a); # ['l', 'e', 'i']
console.log(b); # 'lei'
```

9、sort(): 对数组进行升序排序.
``` js
var a = [1, 0, 3, 2];

a.sort();

console.log(a); # [0, 1, 2, 3]
```
10、 toString(): 将数组转为字符串, 该方法不会修改原数组.
``` js
var a = [1, 2, 3];
var b = a.toString();

console.log(a); # [1, 2, 3]
console.log(b); # "1, 2, 3"

typeof b; # String
```

11、 slice(start, end): 返回索引为start到end(不包括end元素), 创建一个新的数组;
``` js
var a = [1, 2, 3, 4];
var b = a.slice(0, 3);

console.log(a); # [1, 2, 3, 4]
console.log(b); # [1, 2, 3];
```

12、 splice(index, count): 移除从index起的count个元素, 执行后会改变原有数组.
``` js
var a = [1, 2, 3, 4];
var b = a.splice(1, 2);

console.log(a); # [1, 4]
console.log(b); # [2, 3];
```

13、 map(callback): 类似于forEach循环, 以数组中的元素为参数执行callback函数.

``` js
var a = [1, 2, 3, 4];

a.map(function (value, key) {
	console.log('a[' + key + ']' + '=' + value);
});
```