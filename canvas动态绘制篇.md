# canvas动态绘制



---

## 原理

利用`setInterval` 和 `clearRect`制造出一帧一帧的效果。

## 步骤

1. **清空 canvas**
    除非接下来要画的内容会完全充满canvas（例如背景图），否则你需要清空所有。最简单的做法就是用 clearRect 方法。
2. 保存 canvas 状态
如果你要改变一些会改变 canvas 状态的设置（样式，变形之类的），又要在每画一帧之时都是原始状态的话，你需要先保存一下。
3. **绘制动画图形（animated shapes）**
    这一步才是重绘动画帧。
4. 恢复 canvas 状态
如果已经保存了 canvas 的状态，可以先恢复它，然后重绘下一帧。

## 需要用到的API

`setInterval(function, delay)`
当设定好间隔时间后，function会定期执行。
用`clearInterval()清除`
```js
function Draw()
{
    //code....
}
var time = setInterval(Draw,1000/60);
// event
clearInterval(time)
```
`setTimeout(function, delay)`
在设定好的时间之后执行函数


`requestAnimationFrame(callback)`
以浏览器自定义的刷新频率进行刷新，一般为每秒60次
`用cancelAnimationFrame()`清除

```js
function Draw()
{
    //code....
    var time = requestAnimationFrame(Draw);
}

// event
CancelAnimationFrame(time)
```

 
**划重点！！推荐使用requestAnimationFrame做动画，相比起setInterval，他遵循浏览器的刷新速率，使卡顿情况大大下降，同时不因为触发事件而使得动画卡住，大量降低cpu损耗，提高网页运行速率，减少电量损耗，但限于IE8以上，低于IE8的可采用setInterval(function,1000/60)代替**
## 开始绘制

1. 获取宽高

    获取当前canvas的大小
    ```
    window.onload = function(){
        Mycanvas.width = document.getElementById('Mycanvas').offsetWidth;
        Mycanvas.height = document.getElementById('Mycanvas').offsetHeight;
    }
    
    //不要尝试使用 element.style.width / element.style.height
    //canvas不接受css的修改宽高
    ```
    
    如果canvas是一屏式的
    ```js
    window.onload = function(){
    	Mycanvas.width = document.body.clientWidth;
    	Mycanvas.height= document.body.clientHeight;
    }
    ```
    
    如果考虑一些变态玩游戏要调整屏幕大小
    ```js
    window.onresize = function(){
    	Mycanvas.width = document.body.clientWidth;
    	Mycanvas.height= document.body.clientHeight;
    }
    ```

2. 清空画布

    绝大多数情况下，我们都是统一清空画布，然后调用所有的绘制函数
    
    清空:`clearRect(0,0,canvas.width,canvas.height);`

3. 定义对象

    一般来说，每一个物体的运动情况都不同，且自己的绘制方式也不同，所以我们运用对象为其定义属性和方法
    
    ```js
    //面向过程式编程
    var sun = new Image();
    var moon = new Image();
    var earth = new Image();
    function init(){
      sun.src = 'https://mdn.mozillademos.org/files/1456/Canvas_sun.png';
      moon.src = 'https://mdn.mozillademos.org/files/1443/Canvas_moon.png';
      earth.src = 'https://mdn.mozillademos.org/files/1429/Canvas_earth.png';
      window.requestAnimationFrame(draw);
    }
    
    function draw() {
      var ctx = document.getElementById('canvas').getContext('2d');
    
      ctx.globalCompositeOperation = 'destination-over';
      ctx.clearRect(0,0,300,300); // clear canvas
    
      ctx.fillStyle = 'rgba(0,0,0,0.4)';
      ctx.strokeStyle = 'rgba(0,153,255,0.4)';

    
      // Earth
      var time = new Date();
      ctx.rotate( ((2*Math.PI)/60)*time.getSeconds() + ((2*Math.PI)/60000)*time.getMilliseconds() );
      ctx.translate(105,0);
      ctx.drawImage(earth,-12,-12);
    
      // Moon
      ctx.save();
      ctx.rotate( ((2*Math.PI)/6)*time.getSeconds() + ((2*Math.PI)/6000)*time.getMilliseconds() );
      ctx.translate(0,28.5);
      ctx.drawImage(moon,-3.5,-3.5);
      ctx.restore();
    
      ctx.restore();
      
      ctx.beginPath();
      ctx.arc(150,150,105,0,Math.PI*2,false); // Earth orbit
      ctx.stroke();
     
      ctx.drawImage(sun,0,0,300,300);
    
      window.requestAnimationFrame(draw);
    }
    
    init();
    ```
    
    ```js
    //面向对象编程
    var sun = new Image();
    var moon = new Image();
    var earth = new Image();
    sun = {
        src:'https://mdn.mozillademos.org/files/1456/Canvas_sun.png',
        init:function(x,y){
            this.x:x,
            this.y:y
            }
        },
        draw:function(){
            ctx.drawImage(this,0,0,this.x,this.y);
        }
    }
    earth = {
        src:'https://mdn.mozillademos.org/files/1429/Canvas_earth.png',
        init:function(width,height){
            this.width:width,
            this.height:height
            }
        },
        draw:function(){
            var time = new Date();
            ctx.save();
            ctx.translate(150,150);
            ctx.rotate( ((2*Math.PI)/60)*time.getSeconds() + ((2*Math.PI)/60000)*time.getMilliseconds() );
            ctx.translate(105,0);
            ctx.drawImage(this,this.x,this.y);
        }
    }
    moon = {
        src:'https://mdn.mozillademos.org/files/1443/Canvas_moon.png',
        init:function(width,height){
            this.width:width,
            this.height:height,
        }
        draw:function(){
            ctx.save();
            ctx.rotate( ((2*Math.PI)/6)*time.getSeconds() + ((2*Math.PI)/6000)*time.getMilliseconds() );
            ctx.translate(0,28.5);
            ctx.drawImage(this,this.x,this.y);
            ctx.restore();
        }
    }
    earth_orbit = {
        init: function(x,y,r)
        {
            this.x = x;
            this.y = y;
            this.r = r;
        },
        draw:function(){
            ctx.beginPath();
            ctx.arc(this.x,this.y,this.r,Math.PI*2,false); // Earth orbit
            ctx.stroke();
        }
    }
    
    function cleancanvas(){
        ctx.clearRect(0,0,300,300);
    }
    
    function init(){
        sun.init(300,300);
        moon.init(-3.5,-3.5) ;
        earth.init(-12,-12);
        sun.init(150,150,105);
        window.requestAnimationFrame(draw);
    }
    
    function draw() {
            var ctx = document.getElementById('canvas').getContext('2d');
            ctx.globalCompositeOperation = 'destination-over';
            cleancanvas();
            ctx.fillStyle = 'rgba(0,0,0,0.4)';
            ctx.strokeStyle = 'rgba(0,153,255,0.4)';
            earth.draw();
            moon.draw();
            
            ctx.restore();
            
            sun.draw();
            earth_orbit.draw();
            window.requestAnimationFrame(draw);
        }
    
    init();
    ```
    ![image_1c9qamkkv8n8v3m1i2d11u0kg89.png-26kB][1]


  [1]: http://static.zybuluo.com/reader-cyc/1xkr7i29zbix71hmzqqehgn3/image_1c9qamkkv8n8v3m1i2d11u0kg89.png
  
4. 碰撞检测

    在每一次绘制之前进行需要进行的判断，然后改变对象中对应的值  
    以下所有的的代码均可以直接复制到js.jirengu.com以查看效果
    ```html
    //无碰撞检测
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
      <title>JS Bin</title>
    </head>
    <body>
      <canvas id="canvas" width='500' height='500' style="border:1px solid black"></canvas>
    </body>
    <script type="text/javascript">
    	var Mycanvas = document.getElementById('canvas');
    	var ball = {
        init:function(x,y,r,vx,vy,a){
            this.x = x,
            this.y = y,
            this.r = r,
            this.Vx = vx,
            this.Vy = vy,
            this.a = a;
        },
          
        draw:function(){
    
                var ctx = Mycanvas.getContext('2d');
                ctx.beginPath();
       			ctx.arc(this.x,this.y,this.r,0,2*Math.PI,1);
                ctx.fillStyle = '#cc3333';
                ctx.fill();
                ctx.closePath();
                this.x += this.Vx,
                this.y += this.Vy,
                this.Vy += this.a;
     			window.requestAnimationFrame(Draw);
            },
        };
    function Draw(){
    	clearcanvas();
    	ball.draw();
    }
    
    function clearcanvas(){
    	var ctx = Mycanvas.getContext('2d');
    	ctx.clearRect(0,0,Mycanvas.width,Mycanvas.height);
    
    }
    ball.init(50,50,20,4,1,1);
    console.log(ball);
    window.requestAnimationFrame(Draw);
    
    </script>
    </html>
    
    ```
    
    ```js
    //碰撞检测
        <!DOCTYPE html>
        <html>
        <head>
          <meta charset="utf-8">
          <title>JS Bin</title>
        </head>
        <body>
          <canvas id="canvas" width='500' height='500' style="border:1px solid black"></canvas>
        </body>
        <script type="text/javascript">
        	var Mycanvas = document.getElementById('canvas');
        	var ball = {
        	bounce:0,
            init:function(x,y,r,vx,vy,a){
                this.x = x,
                this.y = y,
                this.r = r,
                this.Vx = vx,
                this.Vy = vy,
                this.a = a;
            },
              
            draw:function(){
            		if(this.x >= Mycanvas.width + this.r || this.x <= - this.r )
                    {
                    	/*clearInterval(time);*/
                    	this.bounce = 0;
                    	ball.init(250,50,20,1+Math.random()*3,1,1);
                    	console.log(ball)
                    }
        
            		else
            		{
            			var ctx = Mycanvas.getContext('2d');
        
                    	ctx.beginPath();
           				ctx.arc(this.x,this.y,this.r,0,2*Math.PI,1);
                   		ctx.fillStyle = '#cc3333';
                  		ctx.fill();
                   		ctx.closePath();
        
                    	if(this.y >= Mycanvas.height)
                    	{
                    		this.Vy = Math.min(0,-20 + 5 * this.bounce);
                    		console.log(this.Vy);
                    		this.bounce ++
                   		 }
                   
                    	this.x += this.Vx,
                    	this.y += this.Vy,
                    	this.Vy += this.a;
            		}
                    
        
         	
                },
            };
        function Draw(){
        
        	clearcanvas();
        	ball.draw();
        }
        
        function clearcanvas(){
        	var ctx = Mycanvas.getContext('2d');
        	ctx.clearRect(0,0,Mycanvas.width,Mycanvas.height);
        }
        
        
        ball.init(250,50,20,1+Math.random()*3,1,1);
        
        let time  = setInterval(Draw,16);
        
        
        
        </script>
        </html>
    ```