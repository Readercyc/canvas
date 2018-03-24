# Canvas静态页面绘制（1）

##　what is canvas？

> 
canvas 帆布; 油画（布）(来自有道词典）

- Html5的标签
- 提供一张画布，能运用js代码在其上面进行图形的绘制
- [一个「简单」的canvas实例][1]

## <canvas>标签
```html
<canvas  width="150" height="150"></canvas>
```
- canvas标签看起来与img标签非常类似，但它没有src也没有alt属性，只有width与height
- 初始定义的canvas标签为纯空白
- 可以用css为其添加属性，比如用position为其定位，或是background加底色，或是给它加border让其显示出来
```html
//html
<!DOCTYPE html>
<html>
    <head>
      <meta charset="utf-8">
      <title>JS Bin</title>
    </head>
<style>
    canvas{
      border:1px solid black;
      background-color:#ccc;
    }
    body,html{
      width:100%;
    }
</style>
    <body>
      <canvas height = "300" width = "200"></canvas>
    </body>
</html>
```
- **但是！！！绝对不要用css为其设置宽高！！会使得图像扭曲！！用自带属性！**

## 替换内容
- html5不是所有的浏览器都支持，诸如IE8就不支持
- 所有我们要为其添加一些替换内容，诸如img的alt标签当图片崩掉的时候可以提示用户
```html
<canvas  width="150" height="150">
    您的浏览器不支持canvas！！
</canvas>
<!--不支持<canvas>的浏览器将会忽略容器并在其中渲染后备内容。而支持<canvas>的浏览器将会忽略在容器中包含的内容，并且只是正常渲染canvas。-->
```

## 开始绘制
- 画画需要纸和笔
    2. 找到纸
    1. 拿起一支笔
    2. 画草线
    3. 设置笔的样式
    4. 上色
```html
...
<canvas id = "Mycanvas" width="150" height="150" style="border:1px solid black">
    您的浏览器不支持canvas！！
</canvas>
<script>
    var Mycanvas = document.getElementById("Mycanvas");
    //找到名为canvas的那张纸
    var ctx = Mycanvas.getContext("2d");
    // 设置一只名为ctx的笔，这支笔只在Mycanvas这张纸上有效
    ctx.arc(50,50,30,0,2*Math.PI,True);
    //画草案
    ctx.fillStyle = "rgb(200,0,0)";
    //先设置笔的状态
    ctx.fill();
    //动笔绘画

    ctx.arc(100,100,30,0,2*Math.PI,True);
    //画草案
    ctx.fillStyle = "rgba(0, 0, 200, 0.5)";
    //先设置笔的状态
    ctx.fill();
    //动笔绘画
</script>
    
```
## canvas绘制

### 像素化（栅格化）
![image_1c988hela2nf6u3gmb10qb1vkp26.png-4.3kB][2]

> 
>如上图所示，canvas元素默认被网格所覆盖。通常来说网格中的一个单元相当于canvas元素中的一像素。栅格的起点为左上角（坐标为（0,0））。
>
>所有元素的位置都相对于原点定位。所以图中蓝色方形左上角的坐标为距离左边（X轴）x像素，距离上边（Y轴）y像素（坐标为（x,y））。

###各种各样的路径

- 绘制矩形
    - 特殊:只有矩形不需要绘制路径
    - `fillRect(x,y,width,height )`绘制填充矩形
    - `strokeRect(x,y,width,height) `绘制空心矩形
    - `clearRect(x,y,width,hegiht) `清空矩形
- **路径**
    除了矩形以外，任何的图形绘制都需要以路径作为基础，也就是所谓的草稿，基于路径我们可以为其填充（闭合路径），为其描线等等操作
    1. 首先，你需要创建路径起始点。
    2. 然后你使用画图命令去画出路径。
    3. 之后你把路径封闭。
    4. 一旦路径生成，你就能通过描边或填充路径区域来渲染图形。

- 绘制路径
  `beginPath()`
    新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
`closePath()`
闭合路径之后图形绘制命令又重新指向到上下文中。
`stroke()`
通过线条来绘制图形轮廓,**不会自动闭合路径**
`fill()`
通过填充路径的内容区域生成实心的图形。**自动闭合路径**
    

生成路径的第一步叫做`beginPath()`。本质上，路径是由很多子路径构成，所有的子路径（线、弧形、等等）构成图形。他们就像一个队列一样，存储在一个清单里，直到通过使用`stroke()`或者`fill()`的时候，会将清单中的图形按照笔触当前的样式顺序绘制出来，而使用`closePath()`可将当前清单闭合。

调用`beginPath()`之后，或者canvas刚建的时候，第一条路径构造命令通常被视为是`moveTo（）`，无论实际上是什么。

`closePath()`:就是闭合路径,不是必需的。这个方法会通过绘制一条从当前点到开始点的直线来闭合图形。如果图形是已经闭合了的，即当前点为开始点，该函数什么也不做。

- 绘制线段
    - ` moveTo(x,y)` 将笔触挪到(x,y)
    - ` lineTo(x,y)` 从当前划一条线条到(x,y)

```
// 实心三角形
 ctx.beginPath();
 ctx.moveTo(25,25);
 ctx.lineTo(105,25);
 ctx.lineTo(25,105);
 ctx.fill();

 // 描边三角形
 ctx.beginPath();
 ctx.moveTo(125,125);
 ctx.lineTo(125,45);
 ctx.lineTo(45,125);
 ctx.closePath();
 ctx.stroke();
```
![image_1c98rpeir12pe18o21f951lpb1c7h9.png-3.3kB][3]

- 绘制圆弧
    - `arc(x, y, radius, startAngle, endAngle, anticlockwise)`
    - (x,y)为圆心位置
    - radius为半径
    - startAngle 是 开始的弧度位置
    - endAngle 是结束的弧度位置
    - anticlockwise 是 
![image_1c9au9qfn165ddr81bov1i4f1fgd9.png-7.3kB][4]

- 贝塞尔曲线
    - `quadraticCurveTo(cp1x, cp1y, x, y)`
绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点。
    - `bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`
绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点。
![image_1c9auoeka18tet6p1vlj1ibt1u89.png-5.2kB][5]
    - 控制点即为图上的红点
    - x,y代表的曲线的起始位置和结束位置
    - 比较困难，因为具体的弧度未知，只能靠自己去不断尝试

```
Example
<script>
    var Mycanvas = document.getElementById("Mycanvas");
    //找到名为canvas的那张纸
    var ctx = Mycanvas.getContext("2d");
    for(var i = 1; i <= 100 ; i++)
    {
        ctx.beginPath();
        ctx.lineTo(0,0); 
        ctx.stroke();
        ctx.beginPath();     ctx.arc(Math.random()*400,Math.random()*400,2,0,2*Math.PI);
        ctx.stroke();
    }
</script>
```


  [1]: https://mdn.github.io/canvas-raycaster/
  [2]: http://static.zybuluo.com/reader-cyc/8vishvkg61gl2d5h04vpxqy7/image_1c988hela2nf6u3gmb10qb1vkp26.png
  [3]: http://static.zybuluo.com/reader-cyc/za0xe0o8irjzcndtlz5z4ria/image_1c98rpeir12pe18o21f951lpb1c7h9.png
  [4]: http://static.zybuluo.com/reader-cyc/tv29n7mnvwhdr4ua49ldfnp9/image_1c9au9qfn165ddr81bov1i4f1fgd9.png
  [5]: http://static.zybuluo.com/reader-cyc/ngvlge43ysgff7gmmdehamxp/image_1c9auoeka18tet6p1vlj1ibt1u89.png