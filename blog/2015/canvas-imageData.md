title: Canvas 中的图像处理
date: 2015-06-30
tags: [HTML5, Canvas]
---
HTML5 的Canvas 元素提供了支持图像操作的方法，可以实现对图像的缩放、及制作动画，同时也可以用于游戏中。
Canvas的绘图环境对象4个绘制及操作图像的方法：

<li>drawImage()</li>

<li>getImageData()</li>

<li>putImageData()</li>

<li>createImageData()</li>

### 一、drawImage()

使用该方法可以将图像绘制到canvas中，可以选择绘制图像的整体或局部。这个方法还可以将另外一个canvas中的内容或视频的其中一幀绘制到当前的canvas中。

它的使用方法可接受三种参数形式：
<li>drawImage(image, dx, dy)</li>
<li>drawImage(image, dx, dy, dw, dh)</li>
<li>drawImage(image, sx, sy, sw, wh, dx, dy, dw, dh)</li>

使用该方法将图像绘制到canvas中，所绘的图像为-源图像，而绘制到的地方则为-目标canvas。上面三种方法中的dx、dy分别表示在目标canvas中的x、y轴中的坐标，dw、dh则表示在目标canvas中的宽高。
drawImage()方法会根据源图像的大小及绘制到canvas中的大小比例对图像进行缩放;方法一、二会将图像的整体绘制到画布指定位置上，而方法三则可以将图像的部分或整体绘制到canvas指定位置，也会根据目标区域的宽高对图像进行缩放。

### 二、getImageData()</h4>

调用该方法可以获取图像数据，返回值类型为ImageData对象，该对象包含三个属性：
<li>width: 以设备像素为单位的图像数据宽度</li>
<li>height: 以设备像素为单位的图像数据高度</li>
<li>data: 包含各个设备像素值的数组</li>

data数组中包含的是每个像素的红、绿、蓝、及透明度分量，图像数据的遍历方式：

``` js
var imageData = context.getImageData(0, 0, canvas.width, canvas.height);
```
<!-- more -->
1、遍历每个像素：
``` js
for(var i = 0; i < imageData.length; i++)
  value = imageData[i];
```
2、修改alpha值
``` js
for(var i = 3; i < imageData.length - 4; i +=4)
  imageData[i] = ---;
 ```
3、修改红、绿、蓝分量
``` js
for(var i = 0; i < imageData.length - 4; i +=4)
  imageData[i] = ---;		//red
  imageData[i + 1] = ---;	//green
  imageData[i + 2] = ---;	//blue
```
### 三、putImageData()

这个方法的功能与drawImage()相同，唯一的区别在于两者所操作的对象不同，前者操作的是ImageData，而后者可以操作图像;该方法可以接受7个参数，前三个参数分别指定ImageData、目标canvas上的x轴坐标，目标canvas上的y轴坐标，后4个参数是非必需的，若定义了这四个参数，则会将这四个参数指定的图像区域绘制到目标canvas;

### 四、createImageData()</h4>

使用该方法可以创建一个ImageDate对象，该方法通常用于在操作某一图像数据，而又不改变源图像数据的情况下，作为一个ImageDatad对象的副本使用;

