# Canvas静态页面绘制（2）

## 更改笔触
2. 找到纸（`getElementById("canvas"`)
1. 拿起一支笔(`getContext('2d')`)
2. 画草线(`arc`,`lineTo`,`moveTo`)
3. 设置笔的样式，颜色等(若不设置默认为黑色）
4. 上色（`stroke`,`fill`)

---

- 更改笔触的颜色
    - fillStyle = "color"
    - strokeStyle = "color"

> 一旦您设置了 strokeStyle 或者 fillStyle 的值，那么这个新值就会成为新绘制的图形的默认值。如果你要给每个图形上不同的颜色，你需要重新设置 fillStyle 或 strokeStyle 的值。

```
ctx.moveTo(50,0);

ctx.lineTo(50,50);
ctx.strokeStyle = "orange";

ctx.lineTo(80,100);
ctx.strokeStyle = "#cc3333";

ctx.lineTo(0,150);
ctx.strokeStyle = "#cccc33";

ctx.lineTo(10,200);
ctx.strokeStyle = "#cccccc";

ctx.stroke();
//请问是什么样子的？
```

```
ctx.moveTo(50,0);

ctx.lineTo(50,50);
ctx.strokeStyle = "orange";
ctx.stroke();

ctx.lineTo(80,100);
ctx.strokeStyle = "#cc3333";
ctx.stroke();

ctx.lineTo(0,150);
ctx.strokeStyle = "#cccc33";
ctx.stroke();

ctx.lineTo(10,200);
ctx.strokeStyle = "#cccccc";
ctx.stroke();

//请问是什么样子的？
```

```
ctx.moveTo(50,0);

ctx.beginPath();
ctx.lineTo(50,50);
ctx.strokeStyle = "orange";
ctx.stroke();
ctx.closePath();

ctx.beginPath();
ctx.lineTo(80,100);
ctx.strokeStyle = "#cc3333";
ctx.stroke();
ctx.closePath();

ctx.beginPath();
ctx.lineTo(0,150);
ctx.strokeStyle = "#cccc33";
ctx.stroke();
ctx.closePath();

ctx.beginPath();
ctx.lineTo(10,200);
ctx.strokeStyle = "#cccccc";
ctx.stroke();
ctx.closePath();

//请问是什么样子的？
```

- 透明度
    - rgba
    - `globalAlpha ` 不好用
- 渐变
    - `canvasLinearGradient(x1,y1,x2,y2)` 创建线性渐变
    - `canvasRadialGradient(x1,y1,r1,x2,y2,r2)` 创建放射渐变
    - `addColorStop(position,color)`为渐变上色
    - `createPattern`用图片做渐变
    ```
    var ctx = document.getElementById('canvas').getContext('2d');

  // 创建新 image 对象，用作图案
  var img = new Image();
  img.src = 'images/wallpaper.png';
  img.onload = function(){

    // 创建图案
    var ptrn = ctx.createPattern(img,'repeat');
    ctx.fillStyle = ptrn;
    ctx.fillRect(0,0,150,150);
    ```
    

## 设置笔触的样式
- 笔粗细 `lineWidth = 5` 
- 笔的形状 `lineCap`：`butt`,`round`,`square`
![-][1] 


- 转折处的形状`round`,`bevel`,`miter`
![image_1c9b4kbngpec1rqe12fi1uhvbbjm.png-3.9kB][2]

- 虚线笔 
    - `setLineDash=[x,y]` x是虚线长度，y是空白长度
    - `lineDashOffset = x` 以左上角为基准向左偏移x

- 文字笔 （不推荐多用)
```
var ctx = document.getElementById('canvas').getContext('2d');
ctx.font = "48px Dengxian";
ctx.fillText("Hello world", 10, 50); //实心文本
ctx.strokeText("Hello world", 10, 50); //空心文本
```

    
    

  [1]: http://static.zybuluo.com/reader-cyc/fggm3y8k9vdc7t186ccgv505/image_1c9b4gbi915jj10s6k7o1flvhio9.png
  [2]: http://static.zybuluo.com/reader-cyc/fismumzvx70kcspkpgmnbu6f/image_1c9b4kbngpec1rqe12fi1uhvbbjm.png