title: D3更新和删除元素
date: 2015-07-16 20:20:58
tags: [D3.js]
---
D3通过data()方法很容易的实现数据的绑定，那么当我们绘制好svg元素后，如果数据发生了变化，
比如新增了一个数据元素或是删除了一个数据值，此时需要更新svg元素来表现数据的变化。

以下图为例，点击左边的按钮svg的右边会新增一矩形，点击右边的按钮时svg中則会从右边移除一矩形。<a href="http://codepen.io/yourlei/pen/WvyVaO"><strong>这 里</strong></a>可以看具体效果

<img src="/images/study/update-d3.png" alt="update">

简单分析下触发点击事件时，数据所发生的变化。这个例子中数据保存在dataset数组中，初始时包
含二十个元素，点击左边按钮，会向数组中添加一个元素，数组长度加一;相对应的点击右边按钮时
，会从数组中删除一个元素，数组长度减一;

#### 数据更新——enter()

先来看看如何实现新增一个矩形。看到这你可能会想，这很简单嘛，重新绑定修改后的值，再覆盖
原始值对svg元素的绑定就可以啦！没错，这个方法是可行的，但这种方法比较适合所有值都发生改
变，且数据集的长度不变的情况。显然我们的这个例子不属于这种情况，因为我们的数据并没有全
部发生改变，而且这个例子中数据集的长度发生了变化;这里我们可以这样想，有没有方法可以实现
只对新加入的数据集绑定DOM元素呢，答案是有的。其实这方法很常见，没错，就是它——enter()
.关于这个方法，D3的API上是这样介绍的，为不存在当前DOM元素中的数据找到占位符节点，同时
该方法需要通过搭配append，insert, select，call方法来实例化enter返回的占位符节点。
<!-- more -->
看下面的例子，

##### 原始数据
``` javascript
d3.select("body").selectAll("div")
  .data([4, 8, 15, 16, 23, 42])
  .enter().append("div")
  .text(function(d) { return d; });
```
DOM的结构：<img src="/images/study/20150816-01.jpg">

##### 更新数据后
``` javascript
var div = d3.select("body").selectAll("div")
    .data([1, 2, 4, 8, 16, 32], function(d) { return d; });

    div.enter().append("div")
        .text(function(d) { return d; });
```
DOM的结构：<img src="/images/study/20150816-02.jpg">
对比两次DOM的结构可知，enter()实际仅仅对[1,2,32]这三个新数据添加了占位符节点，旧数据
的DOM结构并没有改变;理解了enter方法后，我们很容易便可实现一开始讲的那个例子中单击按钮
添加一个矩形的效果了。这里是点击事件的回调函数的部分代码：

``` javascript
var value = Math.floor(Math.random()*21 + 5); 
var rect = svg.selectAll('rect')
	.data(dataset)

//console.log( svg.selectAll('rect').data(dataset).enter());

dataset.push(value);
dataset.push(value);

//console.log( svg.selectAll('rect').data(dataset).enter());


	rect.enter()
	.append('tect')
	.attr({
		......
	})
```
<img src="/images/study/update-1.png" alt="update">

代码中打印了数据更新前后调用ernter()返回的结果，看上图的红色箭头处，这里多了两个对象，
其实就是为新加入的数据添加的占位符节点;

#### 删除数据——exit()
API中说这个方法专门用于更新选择，返回更新后不存在于新数据集中的数据元素。还是以enter
中讲的那个例子，先看下更新前后的两组数据：[4, 8, 15, 16, 23, 42],[1, 2, 4, 8, 16, 32],很明显[15,23,4]没有出现在新数据中，因此真正的更新，应该将这三个数据元素从DOM中移除;那么怎么
移除呢？看下面的代码：

##### 移除过期的数据
 ``` javascript
var div = d3.select("body")
.selectAll("div")
.data([1, 2, 4, 8, 16, 32], function(d) { return d; });

div.enter().append("div")
.text(function(d) { return d; });	

div.exit().remove();

console.log(div.exit());

 ```
下图打印了exit的返回值，三个div元素，前面的2,4,5是它们在数据集数组[4, 8, 15, 16, 23, 42]
中的索引,也就是15, 23, 42这三个值;这里要注意是的移除这三个元素并不是exit实现,而是remove
方法做到的,exit的作用是找到这三个元素，然后再交给remove，让它去从DOM中删除元素。

<img src="/images/study/2015-08-16-03.jpg" />
<img src="/images/study/2015-08-16-05.jpg" />
<img src="/images/study/2015-08-16-06.jpg" />
<img src="/images/study/2015-08-16-07.jpg" />

继续看实现点击按钮，移除一个矩形的效果的例子，思路很简单，当点击事件发生时，我们从数据集中移除
一个元素，在选择所有的rect元素绑定data后，调用exit、remove就可以了
``` javascript

dataset.shift();//shitf方法实现从数组头部开始移除一个元素

//更新DOM选择集
var bars = svg.selectAll('rect')
	.data(dataset);

//从DOM中删除过期的数据元素
bars.exit()
	.transition()
	.duration(500)
	.attr({
		'x': width
	})
	.remove();
``` 

