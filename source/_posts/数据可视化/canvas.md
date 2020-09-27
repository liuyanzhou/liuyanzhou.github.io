---
title: canvas
date: 2020-09-13 16:07:35
categories: 数据可视化
top: false
summary: canvas
tags: 
 - 数据可视化
---

### canvas

我们可以使用 canvas 绘制复杂的图形，做动画、处理图像、开发游戏、处理视频等.....

![canvas与普通html+css区别](/medias/imges/dataVisualization/canvas/canvas1.png)

canvas是什么？

广义上：H5新增 canvas 2d绘图功能

在html中：

* canvas 是html标签，我们需要通过用 js 在canvas 里绘制图形
* canvas 可以理解为一张画布

如何设置canvas 的尺寸：

* 设置 `canvas dom`元素的 width 、height 属性
* 使用css设置canvas 尺寸则是相当于将图像进行放大，而不是尺寸。

绘制图形的步骤：

* 扑上画布（canvas）
* 拿起画笔（上下文对象ctx，通过 canvas.getContext('2d')）
* 设置画笔颜色
* 设置图形形状
* 绘制图形

canvas的兼容性：兼容IE9及其以上

canvas 的尺寸限制：

* canvas 的尺寸过大，其中的图像不会显示，推荐控制在 4000以内
* canvas 具体的极限尺寸因浏览器，平台不同而不同

### 一、canvas绘制图形

什么 canvas坐标系、什么是栅格？

* canvas坐标系就是下图的 x、y轴，轴上有刻度，x轴越往右越大，y轴越往下越大，因此零点在左上角，这和html的坐标是一样的。
* 栅格就是下图的4个格子，每一个格子就是一个像素，像素具有 rgba 数据
* 像素的数量等于canvas的宽度乘以高度

![坐标系与栅格](/medias/imges/dataVisualization/canvas/canvas2.png)

#### 绘制矩形

绘制矩形有几种方法：

* 填充矩形方法：fillRect(x,y,w,h)
* 描边矩形方法：strokeRect(x,y,w,h)
* 清理矩形方法：clearRect(x,y,w,h)

> 注意：x,y是绘制矩形的起始点，w，h则是绘制矩形的大小

![矩形](/medias/imges/dataVisualization/canvas/canvas3.png)

实例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>矩形</title>
    <style>
        body{margin:0;overflow: hidden}
        #canvas{background: antiquewhite;}
    </style>
</head>
<body>
<canvas id="canvas"></canvas>
<script>
    const canvas=document.getElementById('canvas');
    canvas.width=window.innerWidth;
    canvas.height=window.innerHeight;
    //画笔
    const  ctx=canvas.getContext('2d');
    //颜色
    ctx.fillStyle='yellow';
    //描边宽度
    ctx.lineWidth=80;
    /*填充矩形方法：fillRect(x,y,w,h)*/
    ctx.fillRect(50,100,400,200);
    /*描边矩形方法：strokeRect(x,y,w,h)*/
    ctx.strokeRect(50,100,400,200);
    /*清理矩形方法：clearRect(x,y,w,h)*/
    ctx.clearRect(50,100,400,200);
</script>
</body>
</html>
```

#### 绘制路径

1. 开始建立路径：ctx.beginPath()

2. 向路径集合中添加子路径：
   * ctx.moveTo(x,y) ： 绘制路径的到那个点，必写
   * ctx.closePath()：路径闭合 ，可选
3. 显示路径:
   * ctx.fill() ：填充，将路径所包围的范围都填充上去
   * ctx.stroke() : 描边
4. 绘制图形

* 直线：ctx.lineTo(x,y);

  * x,y代表画下一个直线点的位置

* 圆弧：ctx.arc(x,y,半径,开始弧度,结束弧度,方向)

  * x,y是圆心，方向是个布尔值，默认为false逆时针画

  ![圆弧](/medias/imges/dataVisualization/canvas/arc.png)

* 切线圆弧：ctx.arcTo(x1,y1,x2,y2,半径)

  * x1,y1是第一个控制点，x2，y2是第二个控制点

  ![切线圆弧](/medias/imges/dataVisualization/canvas/arcTo.png)

* 二次贝塞尔曲线：ctx.quadraticCurverTo(cpx1,cpy1,x,y)

  * cpx1,cpy1控制点1，x,y 结束点

  ![二次贝塞尔曲线](/medias/imges/dataVisualization/canvas/quadraticCurverTo.png)

* 三次贝塞尔曲线：ctx.bezierCurverTo(cpx1,cpy1,cpx2,cpx2,x,y)

  * cpx1,cpy1控制点1，cpx2,cpy2控制点2，x,y结束点

  ![三次贝塞尔曲线](/medias/imges/dataVisualization/canvas/bezierCurverTo.png)

* 矩形;rect(x,y,w,h)

  * x,y是起点坐标，wh是矩形的宽高

实例：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>路径</title>
    <style>
        body {
            margin: 0;
            overflow: hidden
        }
        
        #canvas {
            background: antiquewhite;
        }
    </style>
</head>

<body>
    <canvas id="canvas"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        //画笔
        const ctx = canvas.getContext('2d');

        //描边宽度
        ctx.lineWidth = 10;

        /*
        1.开始建立路径：beginPath()
        2.向路径集合中添加子路径：
        [
            形状; closePath() 可选,
            moveTo(x,y); 形状; closePath() 可选,
            moveTo(x,y); 形状; closePath() 可选,
        ]
        3.显示路径：填充fill() ，描边stroke()
        */

        /*直线：lineTo(x,y); */
        ctx.beginPath();
        ctx.moveTo(50, 50);
        ctx.lineTo(400, 50);
        ctx.lineTo(400, 300);
        // ctx.closePath();
        ctx.stroke();
        // ctx.fill()


        /*arc(x,y,半径，开始弧度，结束弧度，方向)*/
        /*ctx.beginPath();
        ctx.arc(300,300,200,0,Math.PI*3/2);
        ctx.moveTo(300+200,400);
        ctx.arc(300,400,200,0,Math.PI*3/2);
        ctx.stroke();*/


        /*切线圆弧：arcTo(x1,y1,x2,y2,半径)*/
        /*ctx.beginPath();
        ctx.moveTo(50,50);
        ctx.lineTo(400,50);
        ctx.lineTo(400,300);
        ctx.stroke();*/

        /*ctx.beginPath();
        ctx.moveTo(50,50);
        ctx.arcTo(400,50,400,300,200);
        ctx.stroke();*/


        /*二次贝塞尔曲线：quadraCurverTo(cpx1,cpy1,x,y)* /
        // 辅助线
        /*ctx.beginPath();
        ctx.moveTo(50,50);
        ctx.lineTo(400,50);
        ctx.lineTo(400,300);
        ctx.stroke();*/

        /*ctx.beginPath();
        ctx.moveTo(50,50);
        ctx.quadraticCurveTo(400,50,400,300);
        ctx.stroke();*/


        /*三次贝塞尔曲线：bezierCurverTo(cpx1,cpy1,cpx2,cpy2,x,y)*/
        /*ctx.beginPath();
        ctx.moveTo(50,50);
        ctx.lineTo(400,50);
        ctx.lineTo(400,300);
        ctx.lineTo(700,300);
        ctx.stroke();*/

        /*ctx.beginPath();
        ctx.moveTo(50,50);
        ctx.bezierCurveTo(500,100,400,300,700,300);
        ctx.stroke();*/


        /*矩形：rect(x,y,w,h)*/
        /*ctx.beginPath();
        ctx.rect(50,50,400,200);
        ctx.rect(50,300,400,200);
        ctx.stroke();*/
    </script>
</body>
</html>
```

![draw](/medias/imges/dataVisualization/canvas/draw.png)

路径与子路径的概念

路径：

* 路径是子路径的集合
* 一个上下文对象同时只有一个路径，想要绘制新的路径，就要把当前路径置空
* ctx.beginPath() 方法当前路径置空，也就是将路径恢复到默认状态，让之后绘制的路径不受以前路径的影响。

子路径：

* 子路径是一条只有一个起点，连续不断的线
*  ctx.moveTo(x,y) 是设置路径起来点的方法，也是创建一条心的子路径的方法
* 路径里的第一条子路径可以无需设置起点，它的起点默认是子路径中的第一个点

> 注：rect(x,y,w,h)绘制路径时，会具有ctx.moveTo()功能

### 二、图形样式

图形的着色区域有两种：

* 描边区域：strokeStyle 代表了描边样式，描边区域的绘制方法是 stroke() 、strokeRect() 或者 strokeText()
* 填充区域：fillStyle 代表了填充样式，填充区域的绘制方法是 fill()、fillRect() 或者 fillText()

![drawArea](/medias/imges/dataVisualization/canvas/drawArea.png)

图形的着色方式有3种：纯色、渐变、纹理

#### 纯色：

1. 书写方式（与css一致）：red、#000000、rgb(r,g,b)、rbga(r,g,b,a)
2. 赋值方式：ctx.fillStyle = 'red' 或 ctx.strokeStyle = ''rgb(r,g,b)’

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>纯色</title>
        <style>
            body{margin: 0;overflow: hidden}
        </style>
    </head>
    <body>
        <canvas id="canvas"></canvas>
        <script>
            const canvas=document.getElementById('canvas');
            //canvas 充满窗口
            canvas.width=window.innerWidth;
            canvas.height=window.innerHeight;
            //画笔-上下文对象
            const ctx=canvas.getContext('2d');
            //描边宽度
            ctx.lineWidth=40;
            ctx.beginPath();
            ctx.arc(300,200,100,0,Math.PI*2);
            // ctx.fillStyle='red';
            // ctx.fillStyle='#ffff00';
            // ctx.fillStyle='rgb(255,0,0)';
            ctx.fillStyle='rgba(255,0,0,0.5)';
            ctx.fill();

            ctx.beginPath();
            ctx.arc(300,300,100,0,Math.PI*2);
            ctx.fillStyle='rgba(255,0,0,0.5)';
            ctx.fill();
        </script>
    </body>
</html>
```

#### 线性渐变

* 建立渐变对象，定义渐变的区域 ctx.createLinearGradient(x1, y1, x2, y2)
* 为渐变添加颜色节点  addColorStop(position, color)
* 为样式赋值       ctx.fillStyle= 赋值渐对象
* 绘图

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>线性渐变</title>
    <style>
        body{margin: 0;overflow: hidden}
    </style>
</head>
<body>
<canvas id="canvas"></canvas>
<script>
    const canvas=document.getElementById('canvas');
    //canvas 充满窗口
    canvas.width=window.innerWidth;
    canvas.height=window.innerHeight;
    //画笔-上下文对象
    const ctx=canvas.getContext('2d');
    //描边宽度
    ctx.lineWidth=40;
    /*
    1.建立渐变对象，定义渐变的区域
    线性渐变  createLinearGradient(x1, y1, x2, y2)
    */
    const gr=ctx.createLinearGradient(50,50,350,350);
    /*
    2.为渐变添加颜色节点
    addColorStop(position, color)
    */
    gr.addColorStop(0,'red');
    gr.addColorStop(0.5,'yellow');
    gr.addColorStop(1,'green');

    /*
    3.为样式赋值
    */
    ctx.fillStyle=gr;
    /*
    4.绘图
    */
    ctx.fillRect(50,50,300,300);
</script>
</body>
</html>
```

#### 径向渐变

* 建立渐变对象，定义渐变的区域： ctx. createRadialGradient(x1, y1, r1, x2, y2, r2)
* 为渐变添加颜色节点 addColorStop(position, color)
* 为样式赋值       ctx.fillStyle= 赋值渐对象
* 绘图

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>镜像渐变</title>
    <style>
        body {
            margin: 0;
            overflow: hidden
        }
    </style>
</head>
<body>
    <canvas id="canvas"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        //canvas 充满窗口
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        //画笔-上下文对象
        const ctx = canvas.getContext('2d');
        //描边宽度
        ctx.lineWidth = 40;
        /*
        1.建立渐变对象，定义渐变的区域
        径向渐变  createRadialGradient(x1, y1, r1, x2, y2, r2)
        */
        const gr = ctx.createRadialGradient(300, 300, 50, 400, 400, 300);

        /*
        2.为渐变添加颜色节点
        addColorStop(position, color)
        */
        gr.addColorStop(0, 'red');
        gr.addColorStop(0.5, 'yellow');
        gr.addColorStop(1, 'green');
        /*
        3.为样式赋值
        */
        ctx.fillStyle = gr;
        /*
        4.绘图
        */
        ctx.fillRect(0, 0, 700, 700);
    </script>
</body>
</html>
```

#### 纹理

* 建立纹理对象 pattern=context.createPattern(image,"repeat|repeat-x|repeat-y|no-repeat");
* 为着色样式赋值 ctx.fillStyle= pattern 或  ctx.strokeStyle= pattern 
* 为样式赋值
* 绘图

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>纹理</title>
    <style>
        body {
            margin: 0;
            overflow: hidden
        }
    </style>
</head>

<body>
    <canvas id="canvas"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        //canvas 充满窗口
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        //画笔-上下文对象
        const ctx = canvas.getContext('2d');
        //描边宽度
        ctx.lineWidth = 40;
        const img = new Image();
        img.src = './images/floor.jpg';
        img.onload = function() {
            /*
            2.建立纹理对象，定义重复方式及
            context.createPattern(image,"repeat|repeat-x|repeat-y|no-repeat");
            */
            const pat = ctx.createPattern(img, 'repeat-y');
            /*
            3.为样式赋值
            */
            ctx.fillStyle = pat;

            /*
            4.绘图
            */
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }
    </script>
</body>
</html>
```

影响描边样式的因素

* strokeStyle：描边颜色
* lineWidth：描述宽

lineWidth 定义描边的宽度，它是从路径的中心开始绘制的，内外各占宽度的一半

* lineCap：描边端点样式

1. butt （默认） 2. round（圆角）3.square（正方形）

* lineJoin：描边拐角类型

1. miter（尖角，默认）2.round（圆角）3.bevel（折角）

* miterLimit：拐角最大角度（只适用于lineJoin = 'miter'的情况）

当lineJoin为`miter`时，若拐角过小，拐角的厚度就会过大，ctx.miterLimit=1后，可避免此问题

* setLineDash(segments)：将描边设置为虚线，可通过 getLineDash()方法获取虚线样式

ctx.setLineDash([60,90,可多加]) 60间隔下有下划线，90间隔无下划线，多加的情况下也是一段有下划线一段没有下划线

* lineDashOffset：虚线的偏移量

ctx.lineDashOffset = 0(不偏移) | 60（右偏移） |  -60 （左偏移）

#### 投影属性

* 位置：shadowOffsetX/Y 投影的x轴偏移|y轴偏移
* 模糊度：shadowBlur
* 颜色：shadowColor

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>投影</title>
    <style>
        html{height: 100%;overflow: hidden;}
        body{height: 100%;margin: 0;}
    </style>
</head>
<body>
<canvas id="canvas"></canvas>
<script>
    const canvas=document.getElementById('canvas');
    //canvas充满窗口
    canvas.width=window.innerWidth;
    canvas.height=window.innerHeight;
    //画笔
    const  ctx=canvas.getContext('2d');

    /*投影
        位置：shadowOffsetX , shadowOffsetY
        模糊度：shadowBlur
        颜色：shadowColor
    * */
    ctx.beginPath();
    ctx.arc(300,200,100,0,Math.PI*2);
    ctx.fillStyle='#93abff';
    ctx.shadowColor='#000';
    ctx.shadowOffsetX=30;
    ctx.shadowOffsetY=30;
    ctx.shadowBlur=30;
    ctx.fill();
</script>
</body>
</html>
```

注意：

* 投影是上下文对象的一种属性，在绘制图形时，无论执行的是描边方法，还是填充方法，都要在其所绘制图形的后面添加投影
* 在绘制描边图形和填充图形的时候，都需要执行相应的方法，比如路径的fill() 、stroke() 方法。
  而绘制投影时，则不需要任何方法，只要设置相应属性即可，这样在之后绘制图形的时候都会自带投影，无论这个图形是描边图形还是填充图形。

### 三、文本

文本的属性有三种

* 字体：font
* 水平对齐：textAlign
* 垂直对齐：textBaseline

#### font字体

canvas 里的 font 属性和 css 的 font 属性是一样的，它可以设置文本的粗细、字号、字体等。如：

* css 设置字体：p{font:bold 15px arial}
* canavs 实则字体 ctx.font = 'bold 15px arial'

参数与css 的font 是一致的 https://developer.mozilla.org/zh-CN/docs/Web/CSS/font

#### textAlign 水平对齐

| 值                      | 描述                  |      |
| ----------------------- | --------------------- | ---- |
| ctx.textAlign = 'end'   | 文本尾部对齐开始x坐标 |      |
| ctx.textAlign = 'start' | 文本头部对齐开始x坐标 |      |
| ctx.textAlign = 'left'  | 文本左对齐x坐标       |      |
| ctx.textAlign = 'right' | 文本右对齐x坐标       |      |
| ctx.textAlign='center'  | 文本居中对齐x坐标     |      |

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
        }
        
        #canvas {
            background: antiquewhite;
        }
    </style>
</head>

<body>
    <canvas id="canvas"></canvas>
</body>
<script>
    const canvas = document.querySelector('canvas')
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight
    const ctx = canvas.getContext('2d')
     // 辅助线
    ctx.strokeStyle = '#999'
    ctx.moveTo(300, 50)
    ctx.lineTo(300, 600)
    ctx.setLineDash([8])
    ctx.stroke()
    // font 设置字号字体
    ctx.font = '30px Arial'

    /*
        textAlign
        start:基于文本起始位对齐，默认
        end:基于文本结束位对齐
        left：左对齐
        right：右对齐
        center：居中对齐
    */

    ctx.textAlign = 'start'
    ctx.fillText('start', 300, 100)

    ctx.textAlign = 'end'
    ctx.fillText('end', 300, 150)

    ctx.textAlign = 'left'
    ctx.fillText('letf', 300, 200)

    ctx.textAlign = 'right'
    ctx.fillText('right', 300, 250)

    ctx.textAlign = 'center'
    ctx.fillText('center', 300, 300)
</script>

</html>
```

当然start 和end 是会受 html 的dir属性影响

```html
默认情况：文本从左往右顺序排序
<html dir='ltr'>
 文本从右往左顺序排序   
<html dir=‘rtl'>
```

#### textBaseline 垂直对齐

![textBaseline](/medias/imges/dataVisualization/canvas/textBaseline.png)

| 值          | 描述                   |
| ----------- | ---------------------- |
| alphabetic  | 默认。标准字母基线对齐 |
| **top**     | 上对齐                 |
| hanging     | 悬挂基线对齐           |
| **middle**  | 垂直居中               |
| ideographic | 表意基线对齐           |
| bottom      | 下对齐                 |

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
        }
        
        #canvas {
            background: antiquewhite;
        }
    </style>
</head>

<body>
    <canvas id="canvas"></canvas>
</body>

<script>
    const canvas = document.querySelector('canvas')
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight
    const ctx = canvas.getContext('2d')

    /*辅助线*/
    ctx.strokeStyle = '#999';
    ctx.setLineDash([8, 8]);
    ctx.moveTo(30, 200);
    ctx.lineTo(700, 200);
    ctx.stroke();

    ctx.font = '30px blod arial'
    
    ctx.textBaseline = 'top'
    ctx.fillText('照顾自己', 50, 200)

    ctx.textBaseline = 'bottom'
    ctx.fillText('照顾自己', 200, 200)

    ctx.textBaseline = 'middle';
    ctx.fillText('照顾自己', 350, 200);

    ctx.textBaseline = 'hanging';
    ctx.fillText('照顾自己', 550, 200);

    ctx.textBaseline = 'alphabatic';
    ctx.fillText('照顾自己', 750, 200);

    ctx.textBaseline = 'idegraphic';
    ctx.fillText('照顾自己', 950, 200);
</script>
</html>
```

#### 文本的绘制方法

* 填充文字：ctx.fillText(text,x,y,[maxWidth])
* 描边文字：strokeText(text,x,y,[maxWidth])
* 获取文字宽度的方法：measureText(text)

这里的 text 是文本内容，x，y是要绘制的坐标点，maxWidth是设置容纳文字的最大宽度,可选属性

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            body {
                margin: 0;
                overflow: hidden;
            }
            #canvas {
                background: antiquewhite;
            }
        </style>
    </head>

    <body>
        <canvas id="canvas"></canvas>
    </body>
    <script>
        const canvas = document.querySelector('canvas')
        canvas.width = window.innerWidth
        canvas.height = window.innerHeight
        const ctx = canvas.getContext('2d')

        const text = '照顾自己'
        ctx.font = 'bold 150px arial'

        // 获取文字宽度，要在font之后获取
        const {
            width
        } = ctx.measureText(text)
        console.log(width)

        // 颜色
        ctx.fillStyle = 'yellow'
        ctx.strokeStyle = 'maroon'
        // 描边宽度
        ctx.lineWidth = 4
        
        // 描边文字
        ctx.strokeText(text, 50, 200)
        
        // 文字最大宽
        ctx.fillText(text, 50, 200, width)
    </script>
</html>
```

布艺字效果

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            body {
                margin: 0;
                overflow: hidden;
            }

            #canvas {
                background: antiquewhite;
            }
        </style>
    </head>

    <body>
        <canvas id="canvas"></canvas>
    </body>
    <script>
        const canvas = document.querySelector('canvas')
        canvas.width = window.innerWidth
        canvas.height = window.innerWidth
        const ctx = canvas.getContext('2d')

        // 文字内容
        const text = 'canvas'
        // 文字位置
        const [x, y] = [50, 200]
        // 字体属性
        ctx.font = 'bold 200px arial'

        // 投影
        ctx.shadowColor = 'rgba(0,0,0,0.6)'
        ctx.shadowOffsetY = 2
        ctx.shadowBlur = 4

        // 填充文字
        ctx.fillStyle = '#a76921'
        ctx.fillText(text, x, y)

        // 实线描边文字
        ctx.strokeStyle = '#f0d5ac'
        ctx.lineWidth = 8
        ctx.strokeText(text, x, y)

        // 虚线描边文字
        ctx.strokeStyle = '#333'
        ctx.lineWidth = 1
        ctx.setLineDash([5, 3])
        ctx.strokeText(text, x, y)
    </script>
</html>
```

### 四、图像

常用的图像源：图像元素`<img>`、视频元素`<video>`、canvas`<canvas>`

#### 对图片的操作

绘制图像的方法：drawImage()

* 绘图+移动：drawImage(image,x,y)
* 绘图+移动+缩放：drawImage(image,x,y,width,height)
* 绘图+裁切+位移+缩放：drawImage(image,x1,y1,w1,h1,x2,y2,w2,h2)

image 是图像源，x，y是要图像源绘制到canvas的坐标点，width是绘制的宽度，height是绘制的高度，x1，y1，w1,h1是相机对图像源的截取大小，x2，y2,w2,h2是相机将图像源截取下在视口的坐标点与宽高

![drawImage](/medias/imges/dataVisualization/canvas/drawImage.png)

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>绘制图像</title>
        <style>
            body{margin: 0;overflow: hidden}
            #canvas{background: antiquewhite;}
        </style>
    </head>
    <body>
        <canvas id="canvas"></canvas>
        <script>
            const canvas=document.getElementById('canvas');
            canvas.width=window.innerWidth;
            canvas.height=window.innerHeight;
            const ctx=canvas.getContext('2d');

            const img=new Image();
            img.src='./images/dog.jpg';
            img.onload=function(){
                const {width,height}=img;
                /*绘图+移动 drawImage(image, x, y) */
                // ctx.drawImage(img,50,50);

                /*绘图+移动+缩放 drawImage(image, x, y,width,height) */
                // ctx.drawImage(img,0,0,width/2,height);

                /*绘图+裁剪+移动+缩放 drawImage(image, x1, y1,w1,h1,x2,y2,w2,h2) */
                ctx.drawImage(
                    img,
                    //相机视口
                    width/2,height/2,width/2,height/2,
                    //视图矩形
                    50,50,width/2,height/2
                );
            };
        </script>
    </body>
</html>
```

#### 对视频的操作

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>视频</title>
        <style>
            #canvas{background: antiquewhite}
        </style>
    </head>
    <body>
        <video id="vid" controls autoplay loop muted>
            <source src="./images/ripples.mp4" type='video/mp4'>
        </video>
        <div>
            <canvas id="canvas" width="400" height="282"></canvas>
        </div>
        <script>
            //获取图像源
            const vid=document.getElementById('vid');
            //获取canvas
            const canvas=document.getElementById('canvas');
            //获取上下文对象
            const ctx=canvas.getContext('2d');

            //时间监听器
            let interval=null;

            /*视频事件：
                * 视频播放 play
                * 暂停：pause
                * */
            vid.addEventListener('play',function(){
                //绘图
                interval=setInterval(function(){
                    ctx.drawImage(vid,0,0);
                },40);
            });
            vid.addEventListener('pause',function(){
                clearInterval(interval);
            })
        </script>
    </body>
</html>
```

### 五、像素

canvas对于像素的操作方法有

* ImageData() 获取像素信息
* putImageData() 显示像素信息

#### ImageData

ImageData ： 是图片的数据化，他具备以下属性

* data：Uint8ClampedArray[r,g,b,a,r,g,b,a,r,g,b,a,r,g,b,a]
* width：整数
* height：整数

注：Uint8ClampedArray 翻译过来是 8位无符号整型固定数组，其取值范围是[0,255].若小于0则为0，大于255则为255.若为小数，则取整，取整的方法是银行家舍入

怎么拿到 ImageData 对象

1. 直接建立 ImageData()对象（相当于自己新建了一张图片）
   * new ImageDtata()
     * new ImageData(width,height)
     * new ImageData(Uint8ClampedArray,width,height)
   * ctx.createImageData()
     * ctx.createlmageData(width,heidth)
     * ctx.createImageData(ImageData)
2. 获取canvas的 ImageData() 对象（可以以此原来获取真实图片的数据）
   * ctx.getImageData(x,y,width,height)

#### putImageData

在canvas上显示imageData信息，需要通过`ctx.putImageData(ImageData,dx,dy,dirtyX,dirtyY,dirtyWidth,dirtHeight)`

![putImageData](/medias/imges/dataVisualization/canvas/putImageData.png)

图像置灰

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            body {
                margin: 0;
                overflow: hidden
            }

            #canvas {
                background: antiquewhite;
            }
        </style>
    </head>

    <body>
        <canvas id="canvas"></canvas>
    </body>
    <script>
        const canvas = document.getElementById('canvas');
        //canvas充满窗口
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        const ctx = canvas.getContext('2d');

        const img = new Image()
        img.src = './images/river.jpg'
        img.onload = draw

        function draw() {
            const {
                width,
                height
            } = img
            // 1.在canvas中绘制图像
            ctx.drawImage(img, 0, 0)
            // 2.从canvas中获取图像的ImageData
            const imgDt = ctx.getImageData(0, 0, width, height)
            const data = imgDt.data

            // 像素变量
            for (let i = 0; i < data.length; i += 4) {
                let [r, g, b] = [
                    data[i],
                    data[i + 1],
                    data[i + 2]
                ]
                const lm = 0.299 * r + 0.587 * g + 0.114 * b;
                data[i] = lm;
                data[i + 1] = lm;
                data[i + 2] = lm;
            }

            // 3.在canbvas中显示ImageData
            ctx.putImageData(imgDt, 0, height)
        }
    </script>
</html>
```

马赛克效果

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>马赛克效果</title>
        <style>
            body {
                margin: 0;
                overflow: hidden
            }

            #canvas {
                background: antiquewhite;
            }
        </style>
    </head>

    <body>
        <canvas id="canvas"></canvas>
        <script>
            const canvas = document.getElementById('canvas');
            //canvas充满窗口
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            const ctx = canvas.getContext('2d');

            //图像源
            const img = new Image();
            img.src = './images/wns.jpg';
            img.onload = render;

            //色块尺寸
            let size = 5;

            function render() {
                //图像尺寸
                const {
                    width,
                    height
                } = img;

                /*1.在canvas 中绘制图像*/
                ctx.drawImage(img, 0, 0);

                /*2.从canvas 中获取图像的ImageData*/
                const imgDt = ctx.getImageData(0, 0, width, height);
                const data = imgDt.data;
                console.log(data)
                /*行列遍历*/
                for (let y = 0; y < height; y += size) {
                    for (let x = 0; x < width; x += size) {
                        const i = (y * width + x) * 4;
                        let [r, g, b, a] = [
                            data[i],
                            data[i + 1],
                            data[i + 2],
                            data[i + 3],
                        ];
                        const color = `rgba(${r},${g},${b},${a})`;
                        //绘制色块
                        ctx.fillStyle = color;
                        ctx.fillRect(x, y, size, size);
                    }
                }
            }
        </script>
    </body>
</html>
```

### 六、变换

#### canvas的状态管理

上下文对象的状态：就是上下文对象的属性。比如描边颜色，填充颜色，投影，线条样式，变换信息.....

管理上下文状态的方法：

* 保存当前状态：ctx.save()
* 恢复上一次保存状态：ctx.restore()

一般在我们绘制具备同一种样式的图形时，都会用 save() restore() 方法将其包裹起来，这是为了避免当前的图形样式影响以后要绘制的图形样式

状态可以嵌套的，例如

a--save

b--save

restroe() -- 回到b的状态

restore() -- 回到a的状态

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            body {
                margin: 0;
                overflow: hidden
            }

            #canvas {
                background: antiquewhite;
            }
        </style>
    </head>

    <body>
        <canvas id="canvas"></canvas>
    </body>
    <script>
        const canvas = document.querySelector('canvas')
        canvas.width = window.innerWidth
        canvas.height = window.innerHeight
        const ctx = canvas.getContext('2d')
        // 黑
        ctx.fillRect(200, 150, 400, 200)

        ctx.save()
        ctx.fillStyle = 'red'
        // 红
        ctx.fillRect(200, 200, 400, 200)
        ctx.restore()
		// 黑
        ctx.fillRect(200, 450, 400, 200)
    </script>
</html>
```

#### canvas的坐标变换

变换有3个特性

* 移动：translate(x,y)  将坐标的进行水平或垂直移动
* 旋转：rotate(angle) 让坐标轴的原点进行旋转，注意旋转之后坐标轴会被改变
* 缩放：scale(x,y)  让坐标轴的尺度进行缩放

画时钟

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <style>
            body {
                margin: 0;
                overflow: hidden
            }

            #canvas {
                background: antiquewhite;
            }
        </style>
    </head>

    <body>
        <canvas id="canvas"></canvas>
    </body>
    <script>
        const canvas = document.getElementById('canvas');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        const ctx = canvas.getContext('2d');

        //色系
        const [redA, redB, yellow] = ['#db655c', '#d63d46', '#9f8d7d'];

        //一圈的弧度
        const c = Math.PI * 2;

        //canvas 宽高
        const {
            width,
            height
        } = canvas;

        function draw() {
            // 保存状态
            ctx.save()
            // 整体偏移
            ctx.translate(width / 2, height / 2)
            //整体旋转
            ctx.rotate(-Math.PI / 2);

            // 钟表的表框 - 圆弧路径
            // 内框
            ctx.save()
            ctx.strokeStyle = redA
            ctx.lineWidth = 20;
            ctx.beginPath();
            ctx.arc(0, 0, 145, 0, c);
            ctx.stroke();
            ctx.restore()

            // 外框
            ctx.save()
            ctx.strokeStyle = redB;
            ctx.lineWidth = 9;
            ctx.beginPath();
            ctx.arc(0, 0, 155, 0, c);
            ctx.stroke();
            ctx.restore()

            // 刻度
            ctx.save()
            ctx.strokeStyle = redB
            ctx.lineWidth = 15
            ctx.beginPath()
            for (let i = 0; i < 4; i++) {
                ctx.moveTo(90, 0);
                ctx.lineTo(120, 0);
                ctx.stroke();
                ctx.rotate(c / 4);
            }
            ctx.restore()

            // 小时 
            ctx.save()
            ctx.strokeStyle = yellow
            ctx.lineWidth = 6
            ctx.beginPath()
            for (let i = 0; i < 12; i++) {
                if (i % 3 !== 0) {
                    ctx.moveTo(90, 0);
                    ctx.lineTo(120, 0);
                    ctx.stroke();
                }
                ctx.rotate(c / 12);
            }
            ctx.restore()

            // 分钟
            ctx.save()
            ctx.strokeStyle = yellow
            ctx.lineWidth = 3
            ctx.beginPath()
            for (let i = 0; i < 60; i++) {
                if (i % 5 !== 0) {
                    ctx.moveTo(118, 0);
                    ctx.lineTo(120, 0);
                    ctx.stroke();
                }
                ctx.rotate(c / 60);
            }
            ctx.restore()

            // 基于当前时间获取 时、分、秒针的弧度
            const {
                rh,
                rm,
                rs
            } = getRadian()
            // console.log(rh, rm, rs)
            // 时针
            ctx.save()
            ctx.strokeStyle = yellow
            ctx.lineWidth = 9
            ctx.rotate(rh)
            ctx.beginPath();
            ctx.moveTo(-20, 0);
            ctx.lineTo(65, 0);
            ctx.stroke();
            ctx.restore()

            // 分针 
            ctx.save()
            ctx.strokeStyle = yellow
            ctx.lineWidth = 4
            ctx.rotate(rm)
            ctx.beginPath()
            ctx.moveTo(-28, 0)
            ctx.lineTo(80, 0)
            ctx.stroke()
            ctx.restore()
            // 秒针 - 直线 - -30，88，2
            ctx.save();
            ctx.strokeStyle = redB;
            ctx.lineWidth = 2;
            ctx.rotate(rs);
            ctx.beginPath();
            ctx.moveTo(-30, 0);
            ctx.lineTo(88, 0);
            ctx.stroke();
            ctx.restore();

            //圆弧 10
            ctx.save();
            ctx.fillStyle = redB;
            ctx.beginPath();
            ctx.arc(0, 0, 10, 0, Math.PI * 2);
            ctx.fill();
            ctx.restore();

            ctx.restore()
        }

        (function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height)
            draw()
            //请求动画帧
            requestAnimationFrame(render)
        })()



        //基于当前时间获取时、分、秒针的弧度
        function getRadian() {
            //获取当前时间的时分秒 Date
            const date = new Date();

            //当前小时 getHours
            let h = date.getHours();
            if (h > 12) {
                h -= 12
            }
            //当前分钟 getMinutes
            let m = date.getMinutes();
            //当前秒 getSeconds
            let s = date.getSeconds();

            //时针旋转弧度
            const rh = c * h / 12 + c * m / 12 / 60 + c * s / 12 / 60 / 60;
            //分针旋转弧度
            const rm = c * m / 60 + c * s / 60 / 60;
            //秒针旋转弧度
            const rs = c * s / 60;

            //返回三个弧度
            return {
                rh,
                rm,
                rs
            };
        }
    </script>
</html>
```

> 注：有变化的地方往往就有状态管理，对应canvas坐标系进行变换后，以后绘制的图像都会受到这种变换影响。而我们有时会不想让其它图形受到此影响，这样我们就需要用save() 和 restroe() 将变换方法和绘制图形的方法包裹起来。

### 七、动画

启动动画的方法有：`定时器`，`requestAnimationFrame`

* setTimeOut(fn,time) 和 setInterval(fn,time)

  优点：使用方便，动画的时间间隔可以自定义

  缺点：隐藏浏览器标签后，会依旧运行，造成资源浪费，与浏览器刷新频率不同步

* requestAnimationFrane(fn)

  优点：性能更优良，隐藏浏览器标签后，便不会运行，与浏览器刷新频率同步

  缺点：动画的时间间隔无法自定义

对于动画，我们用实例在进行学习：粒子时钟，在我的demo仓库里有例子

### 扩展

使用canvas来对图片进行压缩

```html
<!DOCTYPE html>
<html>

    <head>
        <meta charset="utf-8">
    </head>

    <body>
        <input type="file" id="upload">
        <script>
            const ACCEPT = ['image/jpg', 'image/png', 'image/jpeg'];
            const MAXSIZE = 3 * 1024 * 1024;
            const MAXSIZE_STR = '3MB';

            const upload = document.getElementById('upload');
            upload.addEventListener('change', function(e) {
                const [file] = e.target.files;
                if (!file) {
                    return;
                }
                const {
                    type: fileType,
                    size: fileSize
                } = file;
                if (!ACCEPT.includes(fileType)) {
                    alert(`不支持[${fileType}]文件类型！`);
                    upload.value = '';
                    return;
                } // 图片类型检查
                if (fileSize > MAXSIZE) {
                    alert(`文件超出${MAXSIZE_STR}！`);
                    upload.value = '';
                    return;
                } // 图片容量检查
                // 压缩图片
                convertImageToBase64(file, (base64Image) => compress(base64Image, uploadToServer));
            })
            // 将图片转为base64编码
            function convertImageToBase64(file, callback) {
                let reader = new FileReader();
                reader.addEventListener('load', function(e) {
                    const base64Image = e.target.result;
                    callback && callback(base64Image);
                    reader = null;
                });
                reader.readAsDataURL(file);
            }
            // 对图片进行压缩
            function compress(base64Image, callback) {
                let maxW = 1024;
                let maxH = 1024;
                const image = new Image();
                image.addEventListener('load', function(e) {
                    // 图片的压缩比
                    let ratio; 
                    // 是否需要压缩
                    let needCompress = false; 
                    // 判断上传的图片真实宽度/高度是否超过1024
                    // naturalWidth/naturalHeight 图片的真实宽高
                    if (maxW < image.naturalWidth) {
                        needCompress = true;
                        ratio = image.naturalWidth / maxW;
                        maxH = image.naturalHeight / ratio;
                    } 
                    if (maxH < image.naturalHeight) {
                        needCompress = true;
                        ratio = image.naturalHeight / maxH;
                        maxW = image.naturalWidth / ratio;
                    }
                    // 如果不需要压缩，需要获取图片的实际尺寸
                    if (!needCompress) {
                        maxW = image.naturalWidth;
                        maxH = image.naturalHeight;
                    } 
                    const canvas = document.createElement('canvas');
                    canvas.setAttribute('id', '__compress__');
                    canvas.width = maxW;
                    canvas.height = maxH;
                    canvas.style.visibility = 'hidden';
                    document.body.appendChild(canvas);

                    const ctx = canvas.getContext('2d');
                    ctx.clearRect(0, 0, maxW, maxH);
                    ctx.drawImage(image, 0, 0, maxW, maxH);
                    // toDataURL() 将 canvas画布转化为base64编码的图像
                    const compressImage = canvas.toDataURL('image/jpeg', 0.9);
                    callback && callback(compressImage);
                    canvas.remove();
                });
                image.src = base64Image;
            }

            // 将base64转换为文件，
            // dataurl为base64字符串，filename为文件名（必须带后缀名，如.jpg,.png）
            function dataURLtoFile(dataurl, filename) { 
                var arr = dataurl.split(','),
                    mime = arr[0].match(/:(.*?);/)[1],
                    bstr = atob(arr[1]),
                    n = bstr.length,
                    u8arr = new Uint8Array(n);
                while (n--) {
                    u8arr[n] = bstr.charCodeAt(n);
                }
                return new File([u8arr], filename, {
                    type: mime
                });
            }
            // 图片压缩后要进行的操作，例如上传服务器
            function uploadToServer(compressImage) {
                console.log('upload to server...', compressImage);
                let newImage =  dataURLtoFile(compressImage,'1.jpg')
                console.log(newImage)
            }
        </script>
    </body>

</html>
```

