# Canvas图片操作篇

标签（空格分隔）： 未分类

---
## 图片引入

获取图片
```js
var img = new Image();   // 创建一个<img>元素
img.src = 'myImage.png'; // 设置图片源地址
img.onload = function(){
  // 执行drawImage语句
} // 保证图片加载完之后执行相对应的函数，否则可能会抛出异常

```

一旦获得了源图对象，我们就可以使用 `drawImage` 方法将它渲染到 `canvas` 里。drawImage 方法有三种形态，下面是最基础的一种。

绘制图片
`drawImage(image,x,y)`
其中 `image` 是 image 或者` canvas `对象，`x` 和 `y` 是其在目标 `canvas` 里的起始坐标。
```
drawImage(image,x,y)
//运用此种方式绘制的图片是会被cleanRect给删掉的
//background-image则不会
```

```js
function draw() {
    var ctx = document.getElementById('canvas').getContext('2d');
    var img = new Image();
    img.onload = function(){
      ctx.drawImage(img, 0, 0);
      ctx.beginPath();
      ctx.moveTo(30, 96);
      ctx.lineTo(70, 66);
      ctx.lineTo(103, 76);
      ctx.lineTo(170, 15);
      ctx.stroke();
    }
    img.src = 'images/backdrop.png';
  }
```
![image_1c9nvqhcp3qbj64163cofk1u669.png-4.4kB][1]

`drawImage(image, x, y, width, height)`
这个方法多了2个参数：width 和 height，这两个参数用来控制 当向canvas画入时应该缩放的大小

`drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)`
第一个参数和其它的是相同的，都是一个图像或者另一个 canvas 的引用。其它8个参数最好是参照右边的图解，前4个是定义图像源的切片位置和大小，后4个则是定义切片的目标显示位置和大小。

## 变形

### 保存与恢复

`save()`使用save后当前的状态被保存  
1. 当前的状态包括所有的笔触状态           
2. 例如`strokeStyle`,`fillStyle`..具体参照样式篇
3. 当前canvas使用的各种变形 ，下面会作介绍
4. 当前的裁切路径


`restore()` 使用之后恢复到上一次`save()`的状态，再使用一次就再恢复到上一次save()的状态

```js
function draw() {
  var ctx = document.getElementById('canvas').getContext('2d');

  ctx.fillRect(0,0,150,150);   // 使用默认设置绘制一个矩形
  ctx.save();                  // 保存默认状态

  ctx.fillStyle = '#09F'       // 在原有配置基础上对颜色做改变
  ctx.fillRect(15,15,120,120); // 使用新的设置绘制一个矩形

  ctx.save();                  // 保存当前状态
  ctx.fillStyle = '#FFF'       // 再次改变颜色配置
  ctx.globalAlpha = 0.5;    
  ctx.fillRect(30,30,90,90);   // 使用新的配置绘制一个矩形

  ctx.restore();               // 重新加载之前的颜色状态
  ctx.fillRect(45,45,60,60);   // 使用上一次的配置绘制一个矩形

  ctx.restore();               // 加载默认颜色配置
  ctx.fillRect(60,60,30,30);   // 使用加载的配置绘制一个矩形
}
```  

### 变形

**由于canvas提供的所有变形方法都是针对于整张画布 ，所以必须记得在使用变形画出特定需要的图片之前要记得存储save(),画完之后要立刻restore回到正常样式!之前小雷同学画的拱拱就是这个毛病**

#### 平移
translate 方法，它用来移动 canvas 和它的原点到一个不同的位置。

`translate(x, y)`
translate 方法接受两个参数。x 是左右偏移量，y是上下偏移量，如下图所示。

![image_1c9obl811igqm5874j14cdpn016.png-3.9kB][2]



#### 旋转

rotate 方法，它用于以原点为中心旋转 canvas。

`rotate(angle)`
这个方法只接受一个参数：旋转的角度(angle)，它是顺时针方向的，以弧度为单位的值。
**旋转的中心点始终是 canvas 的原点，如果要改变它，我们需要用到 translate 方法。**

![image_1c9obq9579h815cll7j6sh15dc1j.png-14.5kB][3]

```js
function draw() {
  var ctx = document.getElementById('canvas').getContext('2d');
  ctx.translate(75,75);

  for (var i=1;i<6;i++){ // Loop through rings (from inside to out)
    ctx.save();
    ctx.fillStyle = 'rgb('+(51*i)+','+(255-51*i)+',255)';

    for (var j=0;j<i*6;j++){ // draw individual dots
      ctx.rotate(Math.PI*2/(i*6));
      ctx.beginPath();
      ctx.arc(0,i*12.5,5,0,Math.PI*2,true);
      ctx.fill();
    }

    ctx.restore();
  }
}
```

#### 缩放

`scale(x, y)`
scale 方法接受两个参数。x,y  
分别是横轴和纵轴的缩放因子，它们都必须是正值。值比 1.0 小表示缩小，比 1.0 大则表示放大，值为 1.0 时什么效果都没有。

默认情况下，canvas 的 1 单位就是 1 个像素。举例说，如果我们设置缩放因子是 0.5，1 个单位就变成对应 0.5 个像素，这样绘制出来的形状就会是原先的一半。同理，设置为 2.0 时，1 个单位就对应变成了 2 像素，绘制的结果就是图形放大了 2 倍。
  [1]: http://static.zybuluo.com/reader-cyc/2n191sxfe6qn02x9glnwlu6o/image_1c9nvqhcp3qbj64163cofk1u669.png
  [2]: http://static.zybuluo.com/reader-cyc/c5yc2wm5530mdc0rugsbsrfk/image_1c9obl811igqm5874j14cdpn016.png
  [3]: http://static.zybuluo.com/reader-cyc/0919qbiuellvxdy1z6ihb190/image_1c9obq9579h815cll7j6sh15dc1j.png