title: D3学习笔记之弦图
date: 2015-07-21 20:11:28
tags: [D3.js, Chord Layout]
<!-- images: /images/study/chord.png -->
---
#### 弦图的介绍
D3 Wiki API参考上的介绍说，Chord diagrams 表现一组实体间的关系。文档中举了个例子来说明弦图，假设在一群人中有黑色、金色、棕色、和红色头发的人，而每个人会根据自己的喜好，在这四种头发颜色的人中选择dating partner；比如仅有10%的金色头发的人喜欢和黑色头发的人约会，而有20%的黑色头发的人喜欢和金色头发的人约会；如果用弦图去表示这个假设，则如下图：

<img src="/images/study/chord.png" alt="chord">

图中用到的数据来自下面这个数组，
``` js
population = [
	 [11975,  5871, 8916, 2868],
	 [ 1951, 10048, 2060, 6171],
	 [ 8010, 16145, 8090, 8045],
	 [ 1013,   990,  940, 6907]]
```
将上面的数据理解为一个表格，添加一个表头和左边添加一列，如
<img src="/images/study/chordtable.png" alt="chordtalbe">

这样一来就很好理解了，每一行为一组，在黑色头发那组人中，11975人喜欢和黑色头发的人dating，5871人喜欢和金色头发的人dating，8916人喜欢和棕色头发的人dating，而仅2868人喜欢和红色头发的人dating；
下图做了些标注,有助于理解：
<img src="/images/study/chord3.png" alt="chord3">

弦图中两个组间通过一条弧连接，弧两端的宽度可表现在组中所占的比例，我们可以通过Chord layout提供的方
法对每组中分成的比例进行升序或降序排序，当然也可以对组进行排序；如将上表中的每一行的中的值累加，得到的和就为该组的总人数值，在这个例子中四个组中人数总和的降序为棕色——黑色——金色——红色；
<!-- more -->
#### 弦图的实践
同样是基于上面的例子，制作如下的效果图：
<img src="/images/study/chord4.png" alt="">

##### 关键代码的理解
``` js
chord = d3.layout
  .chord()
  .padding(0.03)
  .sortSubgroups(d3.ascending)
  .sortGroups(d3.descending)	
  .matrix(population),
svg = d3.select('body')
  .append('svg')
  .attr({
	'width': outerRadius*2,
	'height': outerRadius*2
  })
  .append('g')
  .attr({
	'transform': 'translate(' + outerRadius + ',' + outerRadius + ')'
  }),
arc = d3.svg
  .arc()
  .outerRadius(outerRadius)
  .innerRadius(innerRadius),

group = svg.append('g');

// 绘制组
group.selectAll('path')
  .data(chord.groups)
  .enter()
  .append('path')
  .attr({
	  'fill': function(d){
	  	return color(d.index);
	  },
	  'stork': function(d){
	  	return color(d.index);
	  },
	  'd': arc
  });
```