---
title: echarts
date: 2020-09-25 09:57:30
categories: 数据可视化
top: false
summary: echarts
tags: 
 - 数据可视化
---

### Echarts

官网：https://echarts.apache.org/zh/index.html

绘图步骤：

* 建立dom容器
* 引入Echarts
* 实例化echarts对象
* 建立图表配置项
* 显示图表

echart的渲染形式可以是`canvas(默认)`，也可以是`svg`

下面的 null 参数的位置是 echarts 渲染图形所采用的主题，[主题可以从官网下载](https://echarts.apache.org/zh/download-theme.html)

 使用canvas渲染器(默认)

```js
var chart = echarts.init(containerDom,null,{renderer:'canvas'});
// 等价于
var chart = echarts.init(containerDom);
```

使用`SVG`渲染器

```js
var chart = echarts.init(containerDom,null,{renderer:'svg'})
```

### 一、echarts常用组件

常用组件有：标题`title`、图例`legend`、工具栏`toolbox`、提示框`tooltip`、坐标轴：x轴`xAxis`，y轴`yAxis`,系列列表`series`、标记点`markPoint`、标记线`makeLine`

#### 1.1 title:{}

*  主标题：text：string
* 副标题： subtext：string
* 标题边框：borderWidth，borderColor，borderRadius
* 标题位置 ：left、top、right、bottom，值可以是数值也可以是特殊的字符串
* 主标题样式：textStyle ：{} 【官方查阅】
* 副标题样式：subtextStyle ：{}【官方查阅】
* 可见性：show：true（可见，默认） | false （不可见） 

```js
// ...
 title:{
     text:'主标题',
     subtext:'副标题',
     left:'left',
     textStyle:{
       color:'maroon',
       fontSize:24
       },
     subtextStyle:{
       color:'#333',
       fontSize:16
       },
  }       
// ...
```

#### 1.2 图例 legend

图例用于筛选系列，它需要与`series`中的`name属性`搭配使用，默认情况下，`legend`中的data是会根据`series`中系列的`name`自动读取，这里可以省略`data`节点，直接`legend:{}是没有问题的。

常用的属性：

* type  `string`   :   ` plain`(普通图例,缺省就是普通图例) | `scroll`(可滚动翻页的图例，当图例数量较多时可以使用)
* show  `boolean`，默认`true`可见
* left  `string | number` ：  `auto | left | center | right  | 具体数字或百分比`，图例组件里容器作左侧距离，数字或百分比类型是相对于容器狂傲的百分比，`left`等是根据相应的位置自动对齐
* top `string | number ` ：`auto | top | middle | bottom | 具体数字或百分比 `，它相对于容器的大小与 left 属性相似
* right ： `auto | 具体数字或百分比`，同上
* bottom : `auto | 具体数字或百分比 `，同上
* with | height ： `auto  | 具体数字`，图例宽高
* orient `string` : `horizontal` 水平 | `vertical` 竖直，图例列表的布局朝向
* align `string`  :图例标记和文本对齐
* symbolKeepAspect `boolean` : 默认是true , 是否缩放时保持该图形的长宽比
* formatter `string | Function` ： 用来格式化图例文本，支持字符串模板和回调函数两种形式
* textStyle `Object` : 图例的公用文本样式，与 js 写 css 的标识一致， 例如 `fontSize`

```js
// 使用字符串模板，模板变量为图例名称 {name}
formatter: 'Legend {name}'
// 使用回调函数
formatter: function (name) {
    return 'Legend ' + name;
}
```

```js
// ...
legend:{
    data:['难度','人数'],
    formatter:function(name) {
         return 'legend' + name
     }   
},
xAxis:{
  data:['html','css','js','vue']  
},
yAxis:{},
series:[
    {name:'难度',type:'bar',data:[1,2,10,7]},
    {name:'人数',type:'bar',data:[6,6,6,3]}
]
// ...
```

#### 1.3 工具栏 toolbox:{}

具有: 保存图片`saveAsImage`，配置项还原`restore`，数据视图工具`detaView`，数据区域缩放`dataZoom`，动态类型切换`magicType`

常用属性:

| 属性名    | 类型    | 描述                                                         |
| --------- | ------- | ------------------------------------------------------------ |
| show      | boolean | 是否显示工具栏组件                                           |
| orient    | string  | 工具栏icon 的布局朝向，可选`horizontal`(默认，水平) | `vertical` (竖直) |
| itemSize  | number  | 工具栏icon的大小                                             |
| feature   | Object  | 各工具配置项，`saveAsImage(保存图片) | restore (配置项还原)| detaView(数据视图工具) | dataZoom(数据区域缩放) | magicType(动态列表切换) | brush(选框组件的控制按钮) `；也可以自定义工具按钮，详细看官网 |
| iconStyle | Object  | 公用的 icon 样式配置，与 js 写css格式类似                    |
| emphasis  | Object  | 文字相关的配置项                                             |
| tooltip   | Object  | 工具箱的 tooltip 配置，配置项同 [tooltip](https://echarts.apache.org/zh/option.html#tooltip)。默认不显示，可以在需要特殊定制文字样式（尤其是想用自定义 CSS 控制文字样式）的时候开启 tooltip |

```js
// ...
  toolbox:{
    feature:{
      // 保存图片
      saveAsImage: {type:'jpg'},
      // 数据视图工具
      dataView:{},
      // 配置项还原
      restore:{},
      // 数据区域缩放
      dataZoom:{},
      // 动态类型切换
      magicType:{
        type:[
              'line',
               'bar',
               'stack',
               'tiled'
              ]
         }
      },
    }
// ...
```

#### 1.4 提示框 tooltip

提示框组件：它可以设置在多种地方：

* 全局：即`tooltip`
* 坐标系：即:`grid.tooltip、polar.tooltip、single.tooltip`
* 系列中：即`series.tooltip`
* 系列中每个数据项：即`series.data.tooltip`

常用属性:

| 属性名      | 类型                       | 描述                                                         |
| ----------- | -------------------------- | ------------------------------------------------------------ |
| show        | boolean                    | 是否显示提示框组件                                           |
| trigger     | string                     | 触发类型，可选`item`(数据项图形触发，主要在散点图，饼图等无类目轴的图表中)，`axis`:坐标轴触发，主要在柱状图，折线图等会使用类目轴的图表中使用，`none`:什么都不触发 |
| triggerOn   | string                     | mouseover(移入坐标轴触发)、click                             |
| axisPointer | Object                     | 坐标轴指示器配置项，详细看官网                               |
| textStyle   | Object                     | 提示框浮层的文本样式                                         |
| postion     | string \|Array \| Function | 提示框浮层的位置，默认不设置时位置虎跟随鼠标的位置           |
| formatter   | string \| Function         | 提示框浮层内容格式器，支持字符串模板和回调函数两种形式       |

position: string | Array | Function

```
// 绝对位置，相对于容器左侧 10px, 上侧 10 px
position: [10, 10]
// 相对位置，放置在容器正中间
position: ['50%', '50%']


point: 鼠标位置，如 [20, 40]。
params: 同 formatter 的参数相同。
dom: tooltip 的 dom 对象。
rect: 只有鼠标在图形上时有效，是一个用x, y, width, height四个属性表达的图形包围盒。
size: 包括 dom 的尺寸和 echarts 容器的当前尺寸，例如：{contentSize: [width, height], viewSize: [width, height]}。

position: function (point, params, dom, rect, size) {
// 固定在顶部
return [point[0], '10%'];
}
```

实例：tooltip 与 toolbox

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    var mCharts = echarts.init(document.querySelector("div"))
    var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
    var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
    var option = {
      title: {
        text: '成绩展示',
        textStyle: {
          color: 'red'
        },
        borderWidth: 5,
        borderColor: 'blue',
        borderRadius: 5,
        left: 50,
        top: 10
      },
      tooltip: {
        // trigger: 'item'
        trigger: 'axis',
        triggerOn: 'click',
        // formatter: '{b} 的成绩是 {c}'
        formatter: function(arg){
          return arg[0].name + '的分数是:' + arg[0].data
        }
      },
      toolbox: { // 工具箱按钮
        feature: {
          saveAsImage: {}, // 导出图片
          dataView: {}, // 数据视图
          restore: {}, // 重置
          dataZoom: {}, // 区域缩放
          magicType: {
            type: ['bar', 'line']
          } // 动态图表类型的切换
        }
      },
      xAxis: {
        type: 'category',
        data: xDataArr
      },
      yAxis: {
        type: 'value'
      },
      series: [
        {
          name: '语文',
          type: 'bar',
          data: yDataArr
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```

#### 1.5系列列表 series

系列名称 `type`，

系列名`name`用于提示tooltip、图例 legend 筛选，数据更新

数据 data 

标记点 markPoint

标记线 markLine

```js
// ...  
series:[
     {
         name:'学习人数',
         type:'bar',
         data:[30,20,40],
         markPoint:{
             data:[
                 {type:'max'},
                 {type:'min'},
                 // 自定义标记点
                 {coord:[0,30],value:30},
             ]
         }
     },
     {
         name:'就业人数',
         type:'bar',
         data:[33,40,70],
         markLine:{
             data:[
                 {name:'平均线',type:'average'}
             ]
         }
     }
 ]
// ...  
```

自定义渲染系列

```js
{
    // 自定义系列
    type:'custom',
        stack:'总量',
            data:[200],
                renderItem:(params,api) => {
                    // console.log(params,api)
                    const value = api.value(0)
                    const endPoint = api.coord([value,0])
                    return {
                        // 将系列打成一组
                        type :'group',
                        position:endPoint,
                        children:[{
                            type:'path',
                            shape:{
                                // svg 图表放置位
                                d:'M262.4 400l214.4 256c8 11.2 22.4 16 35.2 16s25.6-6.4 35.2-16l214.4-256c8-8 9.6-20.8 4.8-32-4.8-9.6-14.4-17.6-25.6-17.6h-456c-11.2 0-20.8 6.4-25.6 17.6-4.8 11.2-3.2 24 3.2 32z',
                                // x轴偏移
                                x:-6,
                                // y轴偏移
                                y:-20,
                                // 宽
                                width:12,
                                // 高
                                height:12,
                                // 拉伸填充
                                layout:'cover'
                            },
                            style:{
                                fill:'#45c946'
                            }
                        },{
                            type:'path',
                            shape:{
                                d:'M262.4 624l214.4-256c8-11.2 22.4-16 35.2-16s25.6 6.4 35.2 16l214.4 256c8 8 9.6 20.8 4.8 32-4.8 9.6-14.4 17.6-25.6 17.6H284.8c-11.2 0-20.8-6.4-25.6-17.6-4.8-11.2-3.2-24 3.2-32z',
                                x:-6,
                                y:5,
                                width:12,
                                height:12,
                                layout:'cover'
                            },
                            style:{
                                fill:'#45c946'
                            }
                        }]

                    }
                }
}
```

makePoint

data数组中的对象属性有

| 属性名       | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| name         | 名称                                                         |
| value        | 定制标记内容                                                 |
| type         | 值可选 ： min(最小) \| max(最大) \| average(平均值)          |
| coord        | [x,y] 坐标位 ，可用于自定义坐标                              |
| symbolOffset | 标记偏移                                                     |
| symbolSize   | 标记大小                                                     |
| symbol       | 标记图形包括 `circle`、`rect`、`roundRect`、`triangle`、`diamond`、`pin`、`arrow`、`none`、`url` |

### 二、echarts 常用图表

echarts中常用的图标我们有：柱状图`bar`,折线图`line`、饼图`pie`、散点图`scatter`、K线`candlestick`、雷达`radar`、仪表盘`gauge`、地图`map`

#### 2.1 柱状图用法

柱状图描述的是分类数据，呈现的是没一个分类中有多少，通过柱状图，可以很清晰的看出每个分类数据的排名情况

步骤：

* ECharts 最基本的代码结构：引入js文件，DOM容器，初始化对象，设置option
* 准备x轴数据：数组1：['张三','李四',....]
* 准备y轴数据：数组2：[88,92,....]
* 图表类型：在series 下设置`type:bar`

常见效果：

* 标记：最大值，最小值 ---> `markPoint`   平均值 ---> `markLine`
* 显示：数值显示 (label) , 柱宽度 (barWidth) 

**柱状图的展示数据结构是：`[数字|字符,....]`**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    //1. ECharts最基本的代码结构
    //2. x轴数据:['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
    //3. y轴数据:[88, 92, 63, 77, 94, 80, 72, 86]
    //4. 将type的值设置为bar
    var mCharts = echarts.init(document.querySelector("div")) // 初始化echarts实例对象
    var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强'] // 准备x轴数据
    var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86] // 为x轴每一个元素指明数据
    var option = {
      xAxis: {
        type: 'value'
      },
      yAxis: {
        type: 'category',
        data: xDataArr
      },
      series: [
        {
          name: '语文',
          type: 'bar',
          markPoint: { // 标记点
            data: [
              {
                type: 'max', name: '最大值'
              },{
                type: 'min', name: '最小值'
              }
            ]
          },
          markLine: { // 标记线
            data: [
              {
                type: 'average', name: '平均值'
              }
            ]
          },
          label: { // 柱状图上的文字设置
            show: true, // 是否显示
            rotate: 60, // 旋转角度
            position: 'top' // 显示位置
          },
          barWidth: '30%', // 柱的宽度
          data: yDataArr
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```

#### 2.2 折线图用法

折线图主要用来展示数据相随着时间推移的变化趋势，折线图非常适用于展示一个连续的二维数据，如某网站访问人数或商品销量价格的波动

步骤：

* ECharts 最基本的代码结构
* 准备x轴数据，数组1：[’1月',......]
* 准备y轴数据：数组2：[2000,...]
* 图表类型：在series下设置`type:line`

**折线图的展示数据结构：`[数字,....]`**

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>折线</title>
        <style>
            #main {
                margin: 20px;
                width: 700px;
                height: 500px;
            }
        </style>
    </head>

    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            /*基于准备好的dom，初始化echarts实例*/
            const myChart = echarts.init(document.getElementById('main'));

            /*图表配置项*/
            const option = {
                /*x轴
             *   data 类目轴数据
             *   boundaryGap 边界留白
             *   axisLabel 标签
             *       margin 标签偏移量
             * */
                xAxis: {
                    data: ['html', 'css', 'js', 'canvas'],
                    boundaryGap: false,
                },

                /*y轴
                * 脱0显示
                * scale:true
                */
                yAxis: {
                    axisLabel: {
                        margin: 18
                    },
                    // 脱0
                    scale:true
                },
                /*series 系列集合
             *   type 系列类型，line
             *   name 系列名
             *   data 系列数据，[20,10,30,40]
             *   smooth 平滑
             *   stack 堆叠图
             *   areaStyle 区域样式
             *       color 区域颜色
             *   symbolSize 标记点大小
             *   symbol 标记图形
             *       内置形状 'circle', 'rect', 'roundRect', 'triangle', 'diamond', 'pin', 'arrow', 'none'
             *       'image://url' 图片
             *       'path://' svg
             * */
                series: {
                    name: '学习人数',
                    type: 'line',
                    data: [20, 20, 40, 100],
                    smooth: true,
                    areaStyle: {
                        color: '#eeeeff'
                    },
                    itemStyle: {
                        color: 'green'
                    },
                    symbolSize: 30,
                    symbol: 'rect',
                    // symbol: 'image://../images/bs.png',
                }
            };
            
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>
</html>
```

堆叠图

使用 `stack`属性让下一个系列的图基于上一个图继续绘制，记住：两个系列的`stack`的值得是一致的

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height: 400px"></div>
  <script>
    var mCharts = echarts.init(document.querySelector("div"))    
    var xDataArr = ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
    var yDataArr = [3000, 2800, 900, 1000, 800, 700, 1400, 1300, 900, 1000, 800, 600]
    var yDataArr2 = [2000, 3800, 1900, 500, 900, 1700, 2400, 300, 1900, 1500, 1800, 200]
    var option = {
      xAxis: {
        type: 'category',
        data: xDataArr
      },
      yAxis: {
        type: 'value',
      },
      series: [
        {
          type: 'line',
          data: yDataArr,
          stack: 'all', // 堆叠图的设置
          areaStyle: {}
        },
        {
          type: 'line',
          data: yDataArr2,
          stack: 'all', // 堆叠图的设置
          areaStyle: {}
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```
#### 2.3 散点图

散点图通常用来识别两个变量之间的相关性或用来观察他们的关系，从而发现某种趋势，对于查找异常值或理解数据分布也很有效。散点图可以将一个对象的两个变量映射到 x、y位置上。如果此对象还要一个变量，那就可以映射到散点的大小上。

注意点：

x轴与y轴的数据都是**二维数组**

图表类型：在series下设置`type:scatter`,并xAxis与yAxis的type都要设置为`value`

气泡图效果：散点大小不同：symbolSize、散点的颜色不同：itemStyle.color

涟漪动画效果：在series的type改为`effectScatter`，显示时机：`showEffectOn:'render | emphasis'`、动画范围`rippleEffect:{}`

**散点图的展示数据结构：`[[数字,数字,数字],....]`**,二维数组，也可以为`[{name:string,value:值/坐标数组}]`

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>散点图</title>
        <style>
            #main{
                margin: 20px;
                width: 700px;
                height: 500px;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            // 基于准备好的dom，初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));
            //数据
            const data=[
                //x,y, z
                [0, 0, 20],
                [10,10,40],
                [20,10,50],
                [30,30,30],
            ];
            // 指定图表的配置项和数据
            const option = {
                /*x 轴*/
                xAxis:{},
                /*y轴*/
                yAxis:{},
                /*散点图 scatter
        *   data 数据
        *   symbolSize 散点尺寸
        * */
                series:{
                    type:'scatter',
                    data,
                    // symbolSize:20,
                    // param 值的 data 中的数据
                    symbolSize:function(param){
                        return param[2];
                    }
                }
            };

            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>
</html>
```

涟漪效果：

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="lib/echarts.min.js"></script>
    </head>

    <body>
        <div style="width: 600px;height:400px"></div>
        <script> 
            var data = [{
                "gender": "female",
                "height": 161.2,
                "weight": 51.6
            }, {
                "gender": "female",
                "height": 167.5,
                "weight": 59
            }]
            var axisData = []
            for (var i = 0; i < data.length; i++) {
                var height = data[i].height
                var weight = data[i].weight
                var newArr = [height, weight]
                axisData.push(newArr)
            }
            console.log(axisData)
            var mCharts = echarts.init(document.querySelector("div"))
            var option = {
                xAxis: {
                    type: 'value',
                    scale: true
                },
                yAxis: {
                    type: 'value',
                    scale: true
                },
                series: [{
                    // type: 'scatter',
                    type: 'effectScatter', // 指明图表为带涟漪动画的散点图
                    showEffectOn: 'emphasis', // 出现涟漪动画的时机 render emphasis
                    rippleEffect: {
                        scale: 10 // 涟漪动画时, 散点的缩放比例
                    },
                    data: axisData,
                    // symbolSize: 30
                    symbolSize: function(arg) { // 控制散点的大小
                        // console.log(arg)
                        var height = arg[0] / 100
                        var weight = arg[1]
                        // bmi = 体重kg / (身高m*身高m)   大于28,就代表肥胖
                        var bmi = weight / (height * height)
                        if (bmi > 28) {
                            return 20
                        }
                        return 5
                    },
                    itemStyle: { // 控制散点的样式
                        color: function(arg) {
                            // console.log(arg)
                            var height = arg.data[0] / 100
                            var weight = arg.data[1]
                            // bmi = 体重kg / (身高m*身高m)   大于28,就代表肥胖
                            var bmi = weight / (height * height)
                            if (bmi > 28) {
                                return 'red'
                            }
                            return 'green'
                        }
                    }
                }]
            }
            mCharts.setOption(option)
        </script>
    </body>

</html>
```

#### 2.4 饼图

饼图主要用于展现不同类别数值相对于总数的占比情况，图中的扇形的弧长表示该类别的占比大小，所有扇形的弧长的总合为100%，当各类别数据占比较接近时，建议选中柱状图或南丁格尔玫瑰图

画图步骤：

* ECharts 最基本的代码结构
* 准备数据`[{name:xxx,value:xxx},{..},...]`
* 将series上添加一个系列我`type:pie`即可

常见的效果：

* 显示数值：label.formatter
* 圆环：设置两个半径，radius:['50%','70%']
* 南丁格尔图：roseType:'radius'
* 选中效果：选中模式：selectedMode：single | multiple ，选中偏移量 selectedOffset：30

**饼图的展示的数据结构：`[{name:xxx,value:xxx},....]`**,数组对象结构

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    //1. ECharts最基本的代码结构
    //2. 准备数据[{name:???, value:??? },{}]
    //   淘宝: 11231  京东: 22673  唯品会: 6123  1号店: 8989   聚美优品: 6700
    //3. 将type的值设置为pie
    var mCharts = echarts.init(document.querySelector("div"))
    // pieData就是需要设置给饼图的数据, 数组,数组中包含一个又一个的对象, 每一个对象中, 需要有name和value
    var pieData = [
      {
        name: '淘宝',
        value: 11231
      },
      {
        name: '京东',
        value: 22673
      },
      {
        name: '唯品会',
        value: 6123
      },
      {
        name: '1号店',
        value: 8989
      },
      {
        name: '聚美优品',
        value: 6700
      }
    ]
    var option = {
      series: [
        {
          type: 'pie',
          data: pieData,
          label: { // 饼图文字的显示
            show: true, // 显示文字
            //formatter: 'hehe' // 决定文字显示的内容
            formatter: function(arg){
              // console.log(arg)
              return arg.name + '平台' + arg.value + '元\n' + arg.percent + '%'
            }
          },
          // radius: 20 // 饼图的半径
          // radius: '20%' // 百分比参照的是宽度和高度中较小的那一部分的一半来进行百分比设置
          // radius: ['50%', '75%'] // 第0个元素代表的是內圆的半径 第1个元素外圆的半径
          roseType: 'radius', // 南丁格尔图 饼图的每一个区域的半径是不同的
          // selectedMode: 'single' // 选中的效果,能够将选中的区域偏离圆点一小段距离
          selectedMode: 'multiple',
          selectedOffset: 30
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```
#### 2.5 k线图

**K线图的展示数据结构：`[[开盘值, 收盘值, 最低值, 最高值],....]`**,二维数组

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>K线图</title>
        <style>
            #main{
                margin: 20px;
                width: 700px;
                height: 500px;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            // 基于准备好的dom，初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));
            // 指定图表的配置项和数据
            const option = {
                /*x 轴*/
                xAxis:{
                    data:['01','02','03','04']
                },
                /*y轴*/
                yAxis:{},
                /*k 线图 candlestick
        * data [open, close, lowest, highest]-[开盘值, 收盘值, 最低值, 最高值]
        * */
                series:{
                    type:'candlestick',
                    data:[
                        [20,30,10,40],
                        [30,20,10,40],
                        [30,20,0,40],
                        [30,20,0,80],
                    ]
                }
            };

            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>
</html>
```

#### 2.6 雷达图用法

雷达图的每个变量都有一个从中心向外发射的轴线，所有的轴之间的夹角相等，同时每个轴有相同的刻度。雷达图表适合对比变量在数据集内的高低，比如产品性能、排名、评估等。

步骤：

* ECharts 最进本的代码结构：引入js文件，DOM容器，初始化对象，设置option
* 定义个个维度的最大值：`indicator:[{name:'易用性',max:100}]`
* 准备具体产品的数据：`data:[{name:'华为手机',value:[80,90,80,82,90]},...]`
* 图表类型：在series下设置`type:radar`

常用的配置：

* 显示数值：label
* 区域面积：areaStyle
* 绘制类型：shape

两款手机的比较

**雷达图的展示数据结构：[{name:string,value:Array},....]**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    //1. ECharts最基本的代码结构
    //2. 定义各个维度的最大值, 通过radar属性配置
    //   易用性,功能,拍照,跑分,续航, 每个维度的最大值都是100
    //3. 准备产品数据, 设置给series下的data
    //   华为手机1: 80, 90, 80, 82, 90
    //   中兴手机1: 70, 82, 75, 70, 78
    //4. 将type的值设置为radar
    var mCharts = echarts.init(document.querySelector("div"))
    // 各个维度的最大值
    var dataMax = [
      {
        name: '易用性',
        max: 100
      },
      {
        name: '功能',
        max: 100
      },
      {
        name: '拍照',
        max: 100
      },
      {
        name: '跑分',
        max: 100
      },
      {
        name: '续航',
        max: 100
      }
    ]
    var option = {
      radar: { 
        indicator: dataMax, // 配置各个维度的最大值
        shape: 'polygon' // 配置雷达图最外层的图形 circle polygon
      },
      series: [
        {
          type: 'radar', // radar 此图表时一个雷达图
          label: { // 设置标签的样式
            show: true // 显示数值
          },
          areaStyle: {}, // 将每一个产品的雷达图形成阴影的面积
          data: [
            {
              name: '华为手机1',
              value: [80, 90, 80, 82, 90]
            },
            {
              name: '中兴手机1',
              value: [70, 82, 75, 70, 78]
            }
          ]
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```

角色属性对比图：

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>雷达</title>
        <style>
            #main {
                margin: 20px;
                width: 700px;
                height: 600px;
            }
        </style>
    </head>

    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            // 基于准备好的dom，初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));
            /*英雄数据
         *   value [生命，攻击，暴击，防御，速度]
         * */
            const data = [{
                name: '关羽',
                value: [80, 98, 80, 70, 70]
            }, {
                name: '鲁班',
                value: [85, 70, 75, 95, 80]
            }, ];

            // 指定图表的配置项和数据
            const option = {
                /*标题 title*/
                title: {
                    text: '英雄实力对比'
                },

                /*图例 legend
             *   data 数据
             *   orient 排列方式
             *       horizontal 水平，默认
             *       vertical 垂直
             * */
                legend: {
                    data: ['关羽', '鲁班'],
                    left: 'left',
                    top: 40,
                    orient: 'vertical'
                },

                /*
             * 雷达坐标系组件 radar
             *   indicator 雷达图的指示器集合 []
             *       name 指示器名称
             *       min、max 数据区间，实际数据会在此区间内计算比值
             *       color 标签颜色
             *   shape 雷达形状
             *       polygon 多边形，默认
             *       circle 圆形
             *
             * */
                radar: {
                    indicator: [{
                        name: '生命',
                        min: 0,
                        max: 100,
                        color: 'green'
                    }, {
                        name: '攻击',
                        min: 0,
                        max: 100,
                        color: 'maroon'
                    }, {
                        name: '暴击',
                        min: 0,
                        max: 100,
                        color: 'orange'
                    }, {
                        name: '防御',
                        min: 0,
                        max: 100,
                        color: 'black'
                    }, {
                        name: '速度',
                        min: 0,
                        max: 100,
                        color: 'blue'
                    }, ],
                    // shape:'circle'
                },


                /*
             * 雷达 radar
             *   type 图表类型
             *   data 数据 [{name,value[]},...]
             * */
                series: {
                    type: 'radar',
                    data,
                }

            };

            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>

</html>
```

#### 2.7 仪表盘的用法

仪表盘可以更直观的表现出某个坐标的速度或实际情况，如速度、体积、温度、进度、完成率、满意度等

步骤：

* ECharts 最基本的代码结构：引入js文件，DOM容器，初始化对象，设置option
* 准备数据，设置series下的data
* 图表类型：在series下设置`type:gauge`

常用效果

* 数值范围：max、min
* 多个指针：增加data中的数组元素
* 多个颜色差异：itemStyle 

**仪表盘的展示数据结构`[{value:xxx},....]`**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>

  <script>
    //1. ECharts最基本的代码结构
    //2. 准备数据, 设置给series下的data
    //3. 将type的值设置为gauge
    var mCharts = echarts.init(document.querySelector("div"))
    var option = {
      series: [
        {
          type: 'gauge',
          data: [
            {
              value: 97,
              itemStyle: { // 指针的样式
                color: 'pink' // 指针的颜色
              }
            }, // 每一个对象就代表一个指针
            {
              value: 85,
              itemStyle: {
                color: 'green'
              }
            }
          ],
          min: 50 // min max 控制仪表盘数值范围
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>仪表盘</title>
        <style>
            #main{
                margin: 20px;
                width: 700px;
                height: 500px;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            // 基于准备好的dom，初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));
            // 指定图表的配置项和数据
            const option = {
                /*
        * 仪表盘 gauge
        *   type 图表类型
        *   detail 仪表盘详情{formatter:'{value}%'}
        *   data 数据[{name,value},...]
        * */
                series:{
                    type:'gauge',
                    data:[
                        {name:'速度',value:10}
                    ]
                }

            };

            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);

            /*随机修改仪表数值，每隔一秒钟修改一次*/
            setInterval(function(){
                const num=Math.floor(Math.random()*100);
                option.series.data[0].value=num;
                myChart.setOption(option);
            },1000)

        </script>
    </body>
</html>
```

#### 2.8 地图可视化

地图主要用于地理区域数据的可视化

1. 下载地图文件：http://datav.aliyun.com/tools/atlas/#&lat=31.80289258670676&lng=104.2822265625&zoom=4
2. 注册地图：echarts.registerMap('china',data)
3. 配置地图

常见的地图效果：

* 缩放拖动：roam
* 名称显示：label
* 初始缩放比例：zoom
* 地图中心点：center

```js
series:[{
    type:'map',
    map:'china'
}]
```

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>中国地图</title>
        <style>
            #main{
                width: 100%;
                height: 600px;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            //初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));

            /*获取接送文件*/
            fetch('../js/China.json')
                .then((res) => res.json())
                .then(data => {
                //echarts 注册地图 registerMap
                echarts.registerMap('china',data);
                //echarts 配置文件
                const option = {
                    title: {
                        text: '中国地图',
                        left:'center'
                    },
                    /*
                * 地图 map
                *   type 图表类型
                *   map 地图注册名
                * */
                    series:{
                        type:'map',
                        map:'china'
                    }

                };
                //基于配置文件显示图表
                myChart.setOption(option);
            })
        </script>
    </body>
</html>

```

扩展-地图坐标系组件 `geo`

geo是地理坐标系组件，它也可以画地图

geo 和 map 的区别在于，geo支持在地理坐标系上绘制散点图、线集

使用geo画地图的步骤：

* ECharts 最基本的代码结构
* 准备中国地图矢量数据
* 使用`Ajax`获取矢量地图数据
* 在Ajax的回调函数中注册地图矢量数据 `echarts.registerMap('chinaMap',矢量地图数据)`
* 配置geo的type为`map`,map为 `chinaMap`

下面的例子是根据城市的空气质量的不同，显示不同程度的颜色，这是要配置`visualMap`节点，来进行数据的过滤。这是因为`dataZoom`只能用于直角坐标系，而我们非直角坐标系，例如：饼图、地图等，要进行过来可以使用`visualMap`，通过颜色不同来过滤

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
  <script src="lib/jquery.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px;border: 1px solid #f00"></div>

  <script>
    //1. 显示基本的中国地图
    //2. 将空气质量的数据设置给series下的对象
    //3. 将series下的数据和geo关联起来
    //4. 配置visualMap
    var airData = [
        { name: '北京', value: 39.92 },
        { name: '天津', value: 39.13 },
        { name: '上海', value: 31.22 },
        { name: '重庆', value: 66 },
        { name: '河北', value: 147 },
        { name: '河南', value: 113 },
        { name: '云南', value: 25.04 },
        { name: '辽宁', value: 50 },
        { name: '黑龙江', value: 114 },
        { name: '湖南', value: 175 },
        { name: '安徽', value: 117 },
        { name: '山东', value: 92 },
        { name: '新疆', value: 84 },
        { name: '江苏', value: 67 },
        { name: '浙江', value: 84 },
        { name: '江西', value: 96 },
        { name: '湖北', value: 273 },
        { name: '广西', value: 59 },
        { name: '甘肃', value: 99 },
        { name: '山西', value: 39 },
        { name: '内蒙古', value: 58 },
        { name: '陕西', value: 61 },
        { name: '吉林', value: 51 },
        { name: '福建', value: 29 },
        { name: '贵州', value: 71 },
        { name: '广东', value: 38 },
        { name: '青海', value: 57 },
        { name: '西藏', value: 24 },
        { name: '四川', value: 58 },
        { name: '宁夏', value: 52 },
        { name: '海南', value: 54 },
        { name: '台湾', value: 88 },
        { name: '香港', value: 66 },
        { name: '澳门', value: 77 },
        { name: '南海诸岛', value: 55 }
    ]
    var mCharts = echarts.init(document.querySelector("div"))
    $.get('json/map/china.json', function (ret) {
      // ret 就是中国的各个省份的矢量地图数据
      console.log(ret)
      echarts.registerMap('chinaMap', ret)
      var option = {
        geo: {
          type: 'map',
          map: 'chinaMap', // chinaMap需要和registerMap中的第一个参数保持一致
          roam: true, // 设置允许缩放以及拖动的效果
          label: {
            show: true // 展示标签
          }
        },
        series: [
          {
            data: airData,
            geoIndex: 0, // 将空气质量的数据和第0个geo配置关联在一起
            type: 'map'
          }
        ],
        visualMap: {
          min: 0,
          max: 300,
          inRange: {
            color: ['white', 'red'] // 控制颜色渐变的范围
          },
          calculable: true // 出现滑块
        }
      }
      mCharts.setOption(option)
    })

  </script>
</body>

</html>
```

实例：地图与散点图结合:

步骤：

* 给series  下添加一个新的对象
* 准备数据散点数据，配置给`series`下的另一个对象
* 配置series下的新对象的`type:effectScatter`
* 指明散点图的坐标系统为`geo`
* 调整涟漪动画效果

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
  <script src="lib/jquery.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px;border: 1px solid #f00"></div>

  <script>
    //1. 给series下增加一个新的对象
    //2. 准备数据散点数据  , 配置给series下的另外一个对象
    //3. 配置series下的新对象的type值为effectScatter
    //4. 指明散点图的坐标系统为geo
    //5. 调整涟漪动画效果
    var airData = [
      { name: '北京', value: 39.92 },
      { name: '天津', value: 39.13 },
      { name: '上海', value: 31.22 },
      { name: '重庆', value: 66 },
      { name: '河北', value: 147 },
      { name: '河南', value: 113 },
      { name: '云南', value: 25.04 },
      { name: '辽宁', value: 50 },
      { name: '黑龙江', value: 114 },
      { name: '湖南', value: 175 },
      { name: '安徽', value: 117 },
      { name: '山东', value: 92 },
      { name: '新疆', value: 84 },
      { name: '江苏', value: 67 },
      { name: '浙江', value: 84 },
      { name: '江西', value: 96 },
      { name: '湖北', value: 273 },
      { name: '广西', value: 59 },
      { name: '甘肃', value: 99 },
      { name: '山西', value: 39 },
      { name: '内蒙古', value: 58 },
      { name: '陕西', value: 61 },
      { name: '吉林', value: 51 },
      { name: '福建', value: 29 },
      { name: '贵州', value: 71 },
      { name: '广东', value: 38 },
      { name: '青海', value: 57 },
      { name: '西藏', value: 24 },
      { name: '四川', value: 58 },
      { name: '宁夏', value: 52 },
      { name: '海南', value: 54 },
      { name: '台湾', value: 88 },
      { name: '香港', value: 66 },
      { name: '澳门', value: 77 },
      { name: '南海诸岛', value: 55 }
    ]
    var scatterData = [
      {
        value: [117.283042, 31.86119]
      }
    ]
    var mCharts = echarts.init(document.querySelector("div"))
    $.get('json/map/china.json', function (ret) {
      // ret 就是中国的各个省份的矢量地图数据
      console.log(ret)
      echarts.registerMap('chinaMap', ret)
      var option = {
        geo: {
          type: 'map',
          map: 'chinaMap', // chinaMap需要和registerMap中的第一个参数保持一致
          roam: true, // 设置允许缩放以及拖动的效果
          label: {
            show: true // 展示标签
          }
        },
        series: [
          // 用于颜色映射
          {
            data: airData,
            geoIndex: 0, // 将空气质量的数据和第0个geo配置关联在一起
            type: 'map'
          },
          {
            data: scatterData, // 配置散点的坐标数据
            type: 'effectScatter',
            coordinateSystem: 'geo', // 指明散点使用的坐标系统  geo的坐标系统
            rippleEffect: {
              scale: 10 // 设置涟漪动画的缩放比例
            }
          }
        ],
        visualMap: {
          min: 0,
          max: 300,
          inRange: {
            color: ['white', 'red'] // 控制颜色渐变的范围
          },
          calculable: true // 出现滑块
        }
      }
      mCharts.setOption(option)
    })

  </script>
</body>

</html>
```

#### 2.9 实例：疫情折线图

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>西虹市 新增确诊/治愈 趋势</title>
        <style>
            #main {
                margin: 20px;
                width: 700px;
                height: 500px;
            }
        </style>
    </head>

    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            /*基于准备好的dom，初始化echarts实例*/
            const myChart = echarts.init(document.getElementById('main'));
            /*数据*/
            //日期
            const xData = ['3.3', '3.4', '3.5', '3.6', '3.7', '3.8', '3.9'];
            //确诊数据
            const qzData = [200, 170, 90, 80, 30, 40, 10];
            //治愈数据
            const zyData = [10, 20, 40, 70, 120, 145, 150];


            /*指定图表的配置项和数据*/
            const option = {
                /*标题 title {}
             *   主标题 text
             *   副标题 subtext
             *   主标题样式 textStyle
             *       color
             *       fontSize
             * */
                title: {
                    text: '西虹市 新增确诊/治愈 趋势',
                    subtext: '单位：例',
                    textStyle: {
                        fontSize: 16
                    },
                },
                /*提示框 tooltip
             *   trigger 提示框触发方式
             *       item 图形触发，主要在散点图，饼图等无类目轴的图表中使用。
             *       axis 坐标轴触发，主要在柱状图，折线图等会使用类目轴的图表
             *       none 什么都不触发
             *   backgroundColor 背景色
             *   textStyle 文字样式
             *   borderWidth 边界宽度
             *   borderColor 边界颜色
             * */
                tooltip: {
                    trigger: 'axis',
                    backgroundColor: 'white',
                    textStyle: {
                        color: '#333'
                    },
                    borderWidth: 1,
                    borderColor: '#ddd',
                },
                /*x轴
             *   data 类目轴数据
             *   boundaryGap 边界留白
             *   axisLine 轴线
             *       show 可见性
             *   axisLabel 标签
             *       rotate 旋转
             *       margin 外边距
             *   axisTick 刻度
             *       show 可见性
             * */
                xAxis: {
                    boundaryGap: false,
                    data: xData,
                    axisLine: {
                        show: false
                    },
                    axisLabel: {
                        rotate: 50,
                        margin: 15
                    },
                    axisTick: {
                        show: false
                    }
                },
                /*y轴
             *   其属性与x 轴类似
             * */
                yAxis: {
                    type: 'value',
                    axisLine: {
                        show: false
                    },
                    axisLabel: {
                        margin: 15
                    },
                    axisTick: {
                        show: false
                    }
                },
                /*图例 legend
             *   data[] 图例的数据,每一项代表一个系列的 name
             *   icon 图表形状
             *   itemGap 元素间隙
             *   itemHeight 元素高度
             *   textStyle 文字样式
             *       fontSize 大小
             *       color 颜色
             *       padding 内间距
             *   left top right bottom 边界位置
             * */
                legend: {
                    data: ['确诊', '治愈'],
                    icon: 'circle',
                    itemGap: 18,
                    itemHeight: 7,
                    textStyle: {
                        fontSize: 12,
                        color: '#999',
                        padding: [0, 0, 0, -9]
                    },
                    top: 32,
                    left: 'right',
                },
                /*网格 grid
             *   left top right bottom 边界位置
             * */
                grid: {
                    right: 10,
                    left: 50,
                    top: 70
                },
                /*系列列表 series
             *   name 系列名,用于提示tooltip，图例legend 筛选，数据更新
             *   type 列表类型
             *   lineStyle 线的样式
             *       color 颜色
             *   showSymbol 标记点的显示
             *   smooth 线的圆滑
             *   data 数据
             * */
                series: [{
                    name: '确诊',
                    type: 'line',
                    lineStyle: {
                        color: 'crimson',
                    },
                    showSymbol: false,
                    smooth: true,
                    data: qzData
                }, {
                    name: '治愈',
                    type: 'line',
                    lineStyle: {
                        color: 'lightseagreen',
                    },
                    showSymbol: false,
                    smooth: true,
                    data: zyData
                }, ]
            };


            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>

</html>
```

#### 2.10 各地图的数据展示结构

| 图形               | 结构                                                         |
| ------------------ | ------------------------------------------------------------ |
| 柱状图  ('bar')    | [数字\|字符,....]                                            |
| 折线图 ('line')    | [数字,.....]                                                 |
| 散点图 ('scatter') | [[数字,数字,数字],....]，当type = effectScatter 与地图结合时，[{name:string,value:Array(坐标系点)}] |
| 饼图 ('pie')       | [{name: string,value: numvber},....]                         |
| 雷达图 ('radar')   | [{name:string,value:Array},...]                              |
| 仪表盘 ('gauge')   | [{value: number},.....]                                      |
| 地图 ('geo')       | 直接注册地图                                                 |

### 三、echarts 高级应用

学习echarts 的 `多坐标轴`、`异步数据加载`、`数据集`、`区域缩放`、`视觉映射`、`事件`、`富文本标签`、`原生图形组件`、`响应式布局`

#### 3.1 多坐标轴

多坐标轴的常见应用就是一个坐标系有两个y轴

多坐标轴的设置方法：

* 在`yAxis`中写入两组数据，让两组数据的行数保持一致
* 在`series` 中设置数据时，使用`yAxisIndex` 属性设置系列与哪个 y 轴相关联

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>多坐标轴</title>
        <style>
            #main{
                margin: 20px;
                width: 700px;
                height: 500px;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            /*基于准备好的dom，初始化echarts实例*/
            const myChart = echarts.init(document.getElementById('main'));

            /*指定图表的配置项和数据*/
            const option = {
                /*图例*/
                legend:{data:['学习人数','就业人数']},
                /*提示*/
                tooltip:{},
                /*x 轴*/
                xAxis:{
                    data:['html','css','js']
                },

                /*y 轴
                *   name 坐标轴名称
                *   min 刻度最小值
                *   max 刻度最大值
                *   interval 强制设置坐标轴分割间隔
                * */
                yAxis:[
                    {name:'学习人数',min:0,max:50,interval:50/5},
                    {name:'就业人数',min:0,max:900,interval:900/5}
                ],

                /*系列列表 series
                *   yAxisIndex 当前系列对应的y 轴的索引位置
                * */
                //行数：5
                //学习人数：min:0,max:50,interval:50/5
                //就业人数：min:0,max:900,interval:900/5
                series:[
                    {
                        name:'学习人数',
                        yAxisIndex:0,
                        type:'bar',
                        data:[30,20,40],
                    },
                    {
                        name:'就业人数',
                        yAxisIndex:1,
                        type:'bar',
                        data:[330,450,850],
                    }
                ]
            };

            // 基于配置项显示图表。
            myChart.setOption(option);
        </script>
    </body>
</html>
```

#### 3.2 数据更新 | 动画使用

###### 3.2.1、加载动画

加载动画：ECharts 已经内置好了加载数据的动画，我们只需要在合适的时机显示或者隐藏即可。

显示加载动画：`mCharts.showLoading()`

隐藏加载动画：`mCharts.hideLoading()`

###### 3.2.2、增量动画

增量动画的实现方式：`mCharts.setOption()`，所有数据的更新都通过`setOption`实现，不用考虑数据到底产生了那些变化，Echarts会找到两组数据之间的差异然后通过合适的动画去表现数据的变化。

对动画常用的配置项

* 开启动画：`animation:true`
* 动画时长：`animationDuration:5000`，以毫秒为单位，动画持续的时间
* 缓动动画：`animationEasing:bounceOut`，bounceOut只是缓动动画其中的一个动画类型，官网还有更多
* 动画阈值：`animationThreshold:9`，单种形式的元素数量大于这个阈值时会关闭动画

增量动画的例子：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <button id="modify">修改数据</button>
  <button id="add">增加数据</button>
  <script>
    var mCharts = echarts.init(document.querySelector("div"))
    var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
    var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
    var option = {
      xAxis: {
        type: 'category',
        data: xDataArr
      },
      yAxis: {
        type: 'value'
      },
      series: [
        {
          type: 'bar',
          data: yDataArr,
          markPoint: {
            data: [
              {
                type: 'max', name: '最大值'
              },
              {
                type: 'min', name: '最小值'
              }
            ]
          },
          markLine: {
            data: [
              {
                type: 'average', name: '平均值'
              }
            ]
          },
          label: {
            show: true,
            rotate: 60
          },
          barWidth: '30%'
        }
      ]
    }
    mCharts.setOption(option)

    var btnModify = document.querySelector('#modify')
    btnModify.onclick = function () {
      var newYDataArr = [68, 32, 99, 77, 94, 80, 72, 86]
      // setOption 可以设置多次
      // 新的option 和 旧的option
      // 新旧option的关系并不是相互覆盖的关系, 是相互整合的关系
      // 我们在设置新的option的时候, 只需要考虑到变化的部分就可以
      var option = {
        series: [
          {
            data: newYDataArr
          }
        ]
      }
      mCharts.setOption(option)
    }
    var btnAdd = document.querySelector('#add')
    btnAdd.onclick = function(){
      xDataArr.push('小明')
      yDataArr.push(90)
      var option = {
        xAxis: {
          data: xDataArr
        },
        series: [
          {
            data: yDataArr
          }
        ]
      }
      mCharts.setOption(option)
    }
  </script>
</body>

</html>
```

#### 3.3 数据集 dataset

dataset 数据集组件是从 ECharts4 开始有的，用于数据管理

这个没出现之前，我们是将每个系列的数据都写入他的系列配置的data节点下，这样就会出现很大的弊端：

* 不利于多个系列共享一份数据
* 不利于基于原始数据进行图表类型、系列的映射安排
* 造成数据冗余

而dataset 的出现，就是为了解决上面出现的弊端，它的优点：

* 基于原始数据，设置映射关系，就行形成多个图表
* 数据和配置分类，便于单独管理数据
* 数据可以被多个系列或者组件复用
* 支持更多的数据常用格式，例如二维数组、对象数组等。

![dataset]( /medias/imges/dataVisualization/echarts/echarts-dataset.png)

数据集的行列映射：

我们可以给系列配置`seriesLayoutBy`行列映射方式，会影响系列的划分方式

* column：基于列映射，默认
* row：基于行映射

使用dataset数据集的时候，需要特别注意：当我们 dataset 中没有设置维度名列表（dimensions)，那么我们的数据集source中的第一行[默认]（第一列）为维度名，维度名可以理解为我们在系列中写上`series.name`属性，并将其添加到`legend`图例中，其实图例组件默认也是从第一行（第一列读取信息·）

```js
const source=[
    ['大前端','学习人数', '就业人数'],
    ['html',  20,   25],
    ['css',   10,   15],
    ['js',    30,   40]
];
```
![dataset-seriesLayoutBy](/medias/imges/dataVisualization/echarts/dataset-seriesLayoutBy.png)

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>数据集</title>
        <style>
            #main{
                margin: 20px;
                width: 700px;
                height: 800px;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>
        <script>
            // 基于准备好的dom，初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));
            //数据源
            const source=[
                ['大前端','学习人数', '就业人数'],
                ['html',  20,   25],
                ['css',   10,   15],
                ['js',    30,   40]
            ];

            // 指定图表的配置项和数据
            const option = {
                legend: {},
                tooltip: {},
                //数据集
                dataset: {source},
                /*grid [{},{}] 在一个echarts 实例中建立多个grid，并设置其位置
                *   bottom 下边距，如'55%'
                *   top 上边距，如'55%'
                * */
                grid:[
                    {bottom:'55%'},
                    {top:'55%'},
                ],

                /*建立两个x 轴，分属两个网格
                *   type 轴类型，如category
                *   gridIndex 绘图区索引位置
                * */
                xAxis: [
                    {type: 'category',gridIndex:0},
                    {type: 'category',gridIndex:1},
                ],
                /*建立两个y 轴，分属两个网格*/
                yAxis:[
                    {type:'value',gridIndex:0},
                    {type:'value',gridIndex:1},
                ],
                /*
                * series系列
                *   type 图表类型
                *   seriesLayoutBy 行列映射
                *       column 列映射，默认
                *       row  行映射
                *   xAxisIndex x轴索引
                *   yAxisIndex y轴索引
                * */
                series: [
                    {type: 'bar',xAxisIndex:0,yAxisIndex:0},
                    {type: 'bar',xAxisIndex:0,yAxisIndex:0},
                    {type: 'bar',xAxisIndex:1,yAxisIndex:1,seriesLayoutBy:'row'},
                    {type: 'bar',xAxisIndex:1,yAxisIndex:1,seriesLayoutBy:'row'},
                    {type: 'bar',xAxisIndex:1,yAxisIndex:1,seriesLayoutBy:'row'},
                ]
            };
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>
</html>
```

数据集的维度映射

作用：定义数据的维度信息。Echarts 默认会从dataset.source 的第一行/列读取【是第一行/列读取是取决于我们系列的`seriesLayoutBy`的设置，默认是第一行读取，当我们`seriesLayoutBy:row`,则为第一列读取】，但是如果在 `dataset.soucrce`里指定了 `dimensions`,那么echarts 不再会自动从dataset.source 中获取维度信息

```js
dataset:{
    dimensions:['大前端','学习人数','就业人数'],
        source:[
            ['html',200,250],
            ['css',100,150],
            ['js',300,400]
        ]
}
```

**dimensions基于作用范围，可以分为两种：**

* 全局维度：dimensions 写在 dataset 中，作用于所有系列
* 局部维度：dimensions  写在 series 系列中，作用于其所在的系列

```js
// 全局维度
dataset:{
    dimensions:['大前端','html','css'],
        source:[
            ['html',200,250],
            ['css',100,150],
            ['js',300,400]
        ]
}

// 局部维度
series:[
    {
        type:'bar',
        dimensions:['大前端','学习人数','就业人数']
    },
   	{type:'bar'}
]
```

dimensions 中元素的书写方式

* null：不为此处维度作定义
* {type:‘ordinal’}：只定义维度类型，type有以下几种类型
  * number：默认，表示普通数字
  * ordinal：离散型，一般文本使用这种类型，echarts 会自动判断此类型
  * flot：Float64Array 浮点型
  * init：Int32Array 整形
* {name:'good'，type:'number'}：维度名称、维度类型都有
* 'bad'：只指定维度名称，等同于 {name:'bad'}

如：dimensions:[null,{type:'ordinal'},{name:'good',type:'number'},'bad']

**数据集的encode编码映射**

encode 可以定义数据的哪个维度被编码成什么，echarts 默认将第一列的数据映射到x轴上，并且series 里第一个系列对应的就是数据源里的第二列数据。后面的以此类推。那么当我们想要将第二列的数据映射到第一个series系列上该怎么做呢？这时就可以使用 encode 配置，**(特制我们的系列是seriesLayoutBy:colum,若为 row 则把列改为行)**

encode 常见属性：下面的xxx的参数表示：可填维数/维数名

* tooltip：[xxx]，xxx是提示的信息
* seriesNmae：[xxx] ,提示的维数名
* x：x轴的数据映射，可填维数/维数
* y：y轴的数据映射，可填维数/维数名

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>编码映射</title>
    <style>
        #main{
            margin: 20px;
            width: 100%;
            height: 820px;
        }
    </style>
</head>
<body>
<!--建立dom 容器-->
<div id="main"></div>
<!--引入echarts-->
<script src="../js/echarts.min.js"></script>
<script>
    // 基于准备好的dom，初始化echarts实例
    const myChart = echarts.init(document.getElementById('main'));
    //配置项
    let option=null;
    //异步请求数据
    /*
    [
      [
        815,
        34.05,
        351014,
        "Australia",
        1800
      ],...
     ]
    */
    fetch('./lib/table.json')
        .then((res) => res.json())
        .then(data => {
            //显示数据
            show(data);
        });

    function show(data){
        let sizeValue = '57%';
        let symbolSize = 5;
        option = {
            //提示
            tooltip:{},
            /*建立四个绘图区*/
            grid: [
                {right: sizeValue, bottom: sizeValue},
                {left: sizeValue, bottom: sizeValue},
                {right: sizeValue, top: sizeValue,bottom:'10%'},
                {left: sizeValue, top: sizeValue,bottom:'10%'}
            ],
            /*x 轴*/
            xAxis: [
                /*
                * type 坐标轴类型
                *   value  数值轴，适用于连续数据
                *   category 类目轴，适用于离散的类目数据
                *   time 时间轴
                *   log 对数轴
                * gridIndex  x 轴所在的 grid 的索引，默认位于第一个 grid
                * name 坐标轴名称
                * axisLabel 坐标轴刻度标签的相关设置
                *   rotate 刻度标签旋转的角度
                *   interval 坐标轴刻度标签的显示间隔。
                *       0 强制显示所有标签
                *       1 隔一个标签显示一个标签
                * boundaryGap 边界间隙
                *   false 无间隙
                *   true 有间隙
                * */
                {type: 'value', gridIndex: 0, name: '收入', axisLabel: {rotate: 50}},
                {type: 'category', gridIndex: 1, name: '国家',  axisLabel: {rotate: 50,}},
                {type: 'category', gridIndex: 2, name: '国家', axisLabel: {rotate: 50}},
                {type: 'value', gridIndex: 3, name: '寿命', axisLabel: {rotate: 50}}
            ],
            yAxis: [
                {type: 'value', gridIndex: 0, name: '寿命'},
                {type: 'value', gridIndex: 1, name: '收入'},
                {type: 'value', gridIndex: 2, name: '人口'},
                {type: 'value', gridIndex: 3, name: '人口'}
            ],
            dataset: {
                /*
                * dimensions 维度映射 []
                *   string，如 'someName'，等同于 {name: 'someName'}
                *   Object
                *       name  string
                *       type 类型
                *           number，默认，表示普通数据。
                *           ordinal，对于类目、文本这些 string 类型的数据，如果需要能在数轴上使用，须是 'ordinal' 类型
                *           ...
                * */
                dimensions: ['收入','寿命','人口','国家','年'],
                /*数据源*/
                source: data
            },
            series: [
                /*
                * type 图表类型
                *   scatter 散点图
                *   symbolSize 散点大小
                *   xAxisIndex  x 轴索引位
                *   yAxisIndex  y 轴索引位
                *   encode 编码映射
                *       x  x坐标系的维度映射
                *       y  y坐标系的维度映射
                *       tooltip 提示映射
                *   itemStyle 项目样式
                *       opacity 项目透明度
                *
                * */
                {
                    type: 'scatter',
                    symbolSize: symbolSize,
                    xAxisIndex: 0,
                    yAxisIndex: 0,
                    encode: {
                        x: '收入',
                        y: '寿命',
                        tooltip: [0, 1, 2, 3, 4]
                    },
                    itemStyle:{
                        opacity:0.3
                    }
                },
                {
                    type: 'scatter',
                    symbolSize: symbolSize,
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    encode: {
                        x: '国家',
                        y: '收入',
                        tooltip: [0, 1, 2, 3, 4]
                    },
                    itemStyle:{
                        opacity:0.3
                    }
                },
                {
                    type: 'scatter',
                    symbolSize: symbolSize,
                    xAxisIndex: 2,
                    yAxisIndex: 2,
                    encode: {
                        // x: '收入',
                        x: '国家',
                        y: '人口',
                        tooltip: [0, 1, 2, 3, 4]
                    },
                    itemStyle:{
                        opacity:0.3
                    }
                },
                {
                    type: 'scatter',
                    symbolSize: symbolSize,
                    xAxisIndex: 3,
                    yAxisIndex: 3,
                    encode: {
                        x: '寿命',
                        y: '人口',
                        tooltip: [0, 1, 2, 3, 4]
                    },
                    itemStyle:{
                        opacity:0.3
                    }
                }
            ]
        };

        myChart.setOption(option);


    }
</script>
</body>
</html>
```

#### 3.4、直角坐标系常用配置

直角坐标系图表：柱状图、折线图、散点图

常用也是只有在直角坐标系下生效的配置：1、网格：`grid`  2、坐标轴：`axis`  3、区域缩放：`dataZoom`

##### 3.4.1、网格 grid

grid 是用来控制直角坐标系的布局和大小，x轴和y轴就是在 grid 的基础上进行绘制的

| 属性                           | 类型             | 描述                                  |
| ------------------------------ | ---------------- | ------------------------------------- |
| show                           | Boolean          | 显示grid                              |
| borderWidth                    | number           | 网格边框宽度                          |
| borderColor                    | color            | 网格边框颜色                          |
| left \| top \| right \| bottom | string \| number | 网格距离容器的左\|上\|右\|下 侧的距离 |
| width \| height                | string \| number | 网格的宽 \| 高                        |

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="lib/echarts.min.js"></script>
    </head>

    <body>
        <div style="width: 600px;height:400px"></div>
        <script>
            var mCharts = echarts.init(document.querySelector("div"))
            var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
            var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
            var option = {
                grid: { // 坐标轴容器
                    show: true, // 是否可见
                    borderWidth: 10, // 边框的宽度
                    borderColor: 'red', // 边框的颜色
                    left: 120, // 边框的位置
                    top: 120,
                    width: 300, // 边框的大小
                    height: 150
                },
                xAxis: {
                    type: 'category',
                    data: xDataArr
                },
                yAxis: {
                    type: 'value'
                },
                series: [{
                    name: '语文',
                    type: 'bar',
                    markPoint: {
                        data: [{
                            type: 'max',
                            name: '最大值'
                        }, {
                            type: 'min',
                            name: '最小值'
                        }]
                    },
                    markLine: {
                        data: [{
                            type: 'average',
                            name: '平均值'
                        }]
                    },
                    label: {
                        show: true,
                        rotate: 60,
                        position: 'top'
                    },
                    barWidth: '30%',
                    data: yDataArr
                }]
            }
            mCharts.setOption(option)
        </script>
    </body>

</html>
```

##### 3.4.2、 坐标轴 xAxis:{} |  yAxis{}

一个 `grid`中最多有两种位置的 x轴和 y轴

坐标轴类型 type 

* value ： 数值轴，自动会从目标数据中读取数据
* category：类目轴，该类型必须通过data 设置类目数据

显示位置 position 

* xAixs：可取值为 `top `| `bottom`
* yAxis：可取值为`left` | `right`

xAxis：直角坐标系grid中的 x 轴

| 属性                       | 类型                         | 描述                                                         |
| -------------------------- | ---------------------------- | ------------------------------------------------------------ |
| show                       | boolean                      | 是否显示x轴                                                  |
| data                       | Array                        | 类目数据，在type:category，有效。当没有设置type，但设置了`axis.data`则认为，type === 'category' |
| gridIndex                  | number                       | x轴所在的 grid 的索引，默认位于第一个grid                    |
| position                   | string                       | x轴的位置，可选`top | bottom`                                |
| offset                     | number                       | X 轴相对于默认位置的偏移，在相同的 `position` 上有多个 X 轴的时候有用。 |
| type                       | string                       | 坐标轴类型，可选`value | category | time | log`；`value` : 数值轴，适用于连续数据，`category`:类目轴，适合与离散的类目数据，`time`:时间轴，适用于连续的时序数据。`log`:对数轴，适用于对数数据 |
| nameTextStyle              | Object                       | 坐标轴名称的文字样式，太多属性，详细看文档                   |
| boundaryGap                | boolean \|Array              | 坐标轴两边留白策略，类目轴和非类目轴的设置和表现不一样。类目轴可以设置`true|false`,默认为`true`，这时候刻度只是作为分割线，标签和数据点都会在两个刻度组件的带(band)中间；在非类目轴中，包括时间轴、数组，对数轴，`boundaryGap`是一个两个值的数字，分别表示数据最小值和最大值的延伸范围，可以直接设置数值或相对百分比，在设置`min`和`max`后无效果，`boundaryGap: ['20%', '20%']` |
| min \| max                 | number \| string \| Function | 坐标轴刻度最小值 \| 最大值，可以设置特殊值`dataMin|dataMax`,此时该数据在该轴上的最小值\|最大值作为最小 \| 最大刻度，也可以写成一个函数的形式，在里面自动计算 |
| minInterval \| maxInterval | number                       | 自动计算的坐标轴最小 \| 大 间隔大小                          |
| axisLine                   | Object                       | 坐标轴轴线相关设置                                           |
| axisTick                   | Object                       | 坐标轴刻度相关设置                                           |
| axisLabel                  | Object                       | 坐标轴刻度标签的相关配置                                     |

* yAixs 与上方的x轴配置类似。
  * splitNumber 分割段数
  * interval 强制设置坐标轴分割间隔
  * minInterval 坐标轴最小间隔
  * maxInterval 坐标轴最大间隔

小结：**只有type为category类目轴的时候，我们才需要配置data节点**

注意：

要画多个坐标系时，xAxis，yAxis要写成对象的形式，并且要将`grid`写成对应多个，并且在同一个坐标系下的x，y轴要使用同一个`girdIndex`，而在`series`系列下，使用`xAxisIndex、yAxisIndex`来分别指定该系列应该画在哪个x，y轴。这样在series系列中就不用写`gridIndex`对应的绘图区域，因为x，y轴上就已经规定了。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    var mCharts = echarts.init(document.querySelector("div"))
    var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
    var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
    var option = {
      grid: {
        show: true,
        borderColor: 'red',
      },
      xAxis: {
        type: 'category',
        data: xDataArr,
        position: 'top' // 控制坐标轴的位置
      },
      yAxis: {
        type: 'value',
        position: 'right' // 控制坐标轴的位置
      },
      series: [
        {
          name: '语文',
          type: 'bar',
          markPoint: {
            data: [
              {
                type: 'max', name: '最大值'
              },{
                type: 'min', name: '最小值'
              }
            ]
          },
          markLine: {
            data: [
              {
                type: 'average', name: '平均值'
              }
            ]
          },
          label: {
            show: true,
            rotate: 60,
            position: 'top'
          },
          barWidth: '30%',
          data: yDataArr
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```

##### 3.4.1、区域缩放 dataZoom

作用：概览全局，观察细节

dataZoom 组件的类型：

* 内置型数据区域缩放组件（dataZoomInside）：内置于坐标系中，使用户可以在坐标系上通过鼠标拖拽、鼠标滚动，手指滑动（触屏上）来缩放或漫游坐标系
* 滑动条型数据区域缩放组件（dataZoomSlider）：有单独的滑动条，用户在滑动条上进行缩放或漫游
* 框选型数据区域缩放组件（dataZoomSelect）：提供一个选框进行数据区域缩放。即 toolbox.feature.dataZoom,配置项在toolbox中。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    var mCharts = echarts.init(document.querySelector("div"))
    var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
    var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
    var option = {
      dataZoom: [ // 控制区域缩放效果的实现
        {
          type: 'slider', // 缩放的类型  slide代表滑块  inside代表依靠鼠标滚轮
          // type: 'inside'
          xAxisIndex: 0
        },
        {
          type: 'slider',
          yAxisIndex: 0,
          start: 0, // 渲染完成后, 数据筛选的初始值, 百分比
          end: 80 // 渲染完成后, 数据筛选的结束值, 百分比
        }
      ],
      toolbox: {
        feature: {
          dataZoom: {}
        }
      },
      grid: {
        show: true,
        borderColor: 'red',
      },
      xAxis: {
        type: 'category',
        data: xDataArr
      },
      yAxis: {
        type: 'value'
      },
      series: [
        {
          name: '语文',
          type: 'bar',
          markPoint: {
            data: [
              {
                type: 'max', name: '最大值'
              },{
                type: 'min', name: '最小值'
              }
            ]
          },
          markLine: {
            data: [
              {
                type: 'average', name: '平均值'
              }
            ]
          },
          label: {
            show: true,
            rotate: 60,
            position: 'top'
          },
          barWidth: '30%',
          data: yDataArr
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```

#### 3.5 visualMap 视觉映射的原理

visualMap 视觉映射可以让项目的数据和项目的颜色、大小等属性相关联。它的主要作用是通过颜色来对数据进行区分与筛选，这是由于`dataZoom`这个缩放组件只适合用于直角坐标系，而非直角坐标系，饼图，地图等的数据过滤我们可以通过`visualMap`来实现，例如;

```js
soucrce=[
    [1,1,5],
    [2,2,9]
]
```

数据源 source 的第一列和第二列分别对应散点在直角坐标系中的x、y信息，visualMap默认是读取source数据源的最后一列，若我们设置一个从绿色到红色的渐变区间，那么1就对应绿色，9就对应红色，中间的数值就是颜色的过渡值

![visualMap](/medias/imges/dataVisualization/echarts/echarts-visualMap.png)

visualMap的常见属性：

* type映射
  * continuous 连续型
  * piecewise 分段型
* min、max 映射区间的起始位置和结束位置，对应实际数据
* range 显示此范围内的项目，百分百类型
* calculable 是否显示拖拽用的手柄，在连续型的颜色映射器中
* inRange 自定义选中方位中的视觉元素
  * color:[]颜色映射
  * symbolSize 大小映射
  * .... 看文档

注意：visualMap 以前叫`dataRange` 

必要设置的属性：

* min | max | inRange | calculable 
* 在绘制根据空气数值不同显示不同的颜色的实例上有运用该属性。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>视觉映射</title>
    <style>
        #main{
            margin: 20px;
            width: 800px;
            height: 500px;
        }
    </style>
</head>
<body>
<!--建立dom 容器-->
<div id="main"></div>
<!--引入echarts-->
<script src="../js/echarts.min.js"></script>
<script>
    // 基于准备好的dom，初始化echarts实例
    const myChart = echarts.init(document.getElementById('main'));

    //数据源
    const source = [
       //x   y   z   w
        [2,  1, 10, 90],
        [4,  2, 20, 80],
        [6,  3, 30, 70],
        [8,  4, 40, 60],
        [10, 5, 50, 50],
        [12, 6, 60, 40],
        [14, 7, 70, 30],
        [16, 8, 80, 20],
        [18, 9, 90, 10],
    ];

    //颜色范围
    const color=['#70ad47', '#c00000'];

    // 指定图表的配置项和数据
    const option = {
        tooltip: {},
        /*绘图区*/
        grid:{
            left:100
        },
        /*x 轴*/
        xAxis: {},
        /*y 轴*/
        yAxis: {},
        /*数据集*/
        dataset:{source},
        /*
        * visualMap 视觉映射 {}
        *   type 映射方式
        *       continuous 连续型
        *       piecewise 分段型
        *   min 映射区间的起始位置，如0
        *   max 映射区间的结束位置，如90
        *   range [] 显示此范围内的项目（在连续型中有效），百分百类型，如[0,100]
        *   calculable 是否显示拖拽用的手柄
        *   inRange 自定义映射范围
        *       color[] 颜色映射
        *       symbolSize[] 大小映射
        *
        * */
        visualMap:{
            type:'continuous',
            // type:'piecewise',
            min:0,
            max:100,
            range:[0,90],
            calculable: true,
            inRange:{
                color,
                symbolSize:[10,60],
            },
            // dimension:2,
            dimension:3
        },

        /*系列列表*/
        series: [
            {
                name: '视觉映射',
                type: 'scatter',
                encode:{
                    tooltip:[0,1,2]
                }
            },
        ]
    };
    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);
</script>
</body>
</html>
```

#### 3.6 富文本标签

富文本标签，就是内容丰富的文本标签，在许多地方（如图、轴的标签等）都可以使用富文本标签。

富文本的实现步骤：

* 首先要确定一个承载富文本的载体，比如提示、标签等
* 要有`formatter`，这个东东可以理解为html标签，只不写法是完全不一样的，如：

```
formatter:'{a|样式 a}\n'+'{b|样式 b}\n'+'默认样式{x|样式 x}'
// 就相当于
<span class="a">样式a</span>
<span class="b">样式b</span>
默认样式<span class="x">x</span>
```

* 设置文本样式：[rich](https://echarts.apache.org/zh/option.html#series-bar.label.rich)

```js
rich:{
    a:{
        color:'red'
    },
    b:{
        backgroundColor:{
            image:'xxx/xxx.jpg'
        },
       height:40
    },
     x:{
         fontSize:18
     }
}
```

实例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>富文本</title>
    <style>
        #main{
            margin: 20px;
            width: 700px;
            height: 500px;
        }
    </style>
</head>
<body>
<!--建立dom 容器-->
<div id="main"></div>
<!--引入echarts-->
<script src="../js/echarts.min.js"></script>
<script>
    // 基于准备好的dom，初始化echarts实例
    const myChart = echarts.init(document.getElementById('main'));

    // 数据
    const data=[
        {name:'杨戬',value:80,img:'./images/yj.jpg'},
        {name:'鲁班',value:60,img:'./images/lb.jpg'},
        {name:'沈梦溪',value:40,img:'./images/smx.jpg'},
        {name:'诸葛亮',value:30,img:'./images/zgl.jpg'}
    ];

    //获取hero的数据
    let hero=data[0];

    /*自定义标签 label
    *   formatter 文本块
    *       '{样式名|文字内容}\n 换行'
    *   文本块的样式
    *       textBorderColor 文本描边颜色
    *       textBorderWidth 文本描边宽度
    *       ...
    *   rich 富文本，在其中写入样式
    *       width 宽
    *       height 高
    *       backgroundColor 背景色
    *           image 背景图
    *       fontSize 文字大小
    *       lineHeight 行高
    *       fontWeight 文本加粗
    *       ...
    * */
    data.forEach((hero,ind)=>{
        hero.label={
            formatter:'{img|}\n{name|'+hero.name+'}\n{val|战力：'+hero.value+'}',
            textBorderColor:'yellow',
            textBorderWidth:2,
            rich:{
                img:{
                    width:60,
                    height:60,
                    backgroundColor:{
                        image:hero.img
                    }
                },
                name:{
                    fontSize:16,
                    lineHeight:28,
                    fontWeight:'bold'
                },
                val:{}
            }
        };
    });


    /*配置项*/
    const option = {
        title:{text:'英雄战力'},
        series: {
            type: 'pie',
            data,
            // 饼图半径             
            radius:'70%',
        }
    };
    myChart.setOption(option);
</script>
</body>
</html>
```

#### 3.7 原生图形组件的基本概念

原生图形组件就是可以`自定义图形`的组件，在原生组件里绘制的图形，可以`绑定鼠标事件`、`拖拽事件`等。

echarts 有两种点位：坐标位，像素位

坐标位有直角坐标位、地理坐标位等。原生图形的位置就是基于像素位定位的。

echarts 实例对象提供了坐标位和像素位的转换方法

* covertToPixel(坐标系，[x,y]) 坐标位转像素位
* covertFromPixel(坐标系，[x,y])像素位转坐标位

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>原生图形</title>
    <style>
        #main{
            margin: 20px;
            width: 700px;
            height: 700px;
            background: antiquewhite;
        }
    </style>
</head>
<body>
<!--建立dom 容器-->
<div id="main"></div>
<!--引入echarts-->
<script src="../js/echarts.min.js"></script>
<script>
    /*实例化echarts*/
    const myChart = echarts.init(document.getElementById('main'));

    /*绘制坐标系*/
    myChart.setOption({
        /*x轴
        *   min,max 刻度区间
        *   type 坐标轴的类型
        *       value 数值轴
        * */
        xAxis:{
            min:0,
            max:50,
        },

        /*y 轴
        *   属性同x
        * */
        yAxis:{
            min:0,
            max:50,
        }
    });

    /*绘制原生图形*/
    myChart.setOption({
        graphic:{
            id:'c',
            type:'circle',
            shape:{
                r:100
            },
            // position:[10,10],
            position:myChart.convertToPixel('grid',[20,10]),
            style:{
                fill:'green'
            },
        }
    });

</script>
</body>
</html>
```

#### 3.8 响应式布局

在html中使用 css 中的 flex 可以轻松实现响应式布局，在echarts里，如何适配不同尺寸的屏幕呢？

* 简单点的可以通过尺寸、位置等属性设置百分比来实现
* 复杂写的就需要自定义响应规则

定义自定义响应规则的步骤方法：

* 建立基础配置项 baseOption
* 建立规则配置列表 media
* 建立query条件规则
* 建立次规则下的配置信息 option
* echarts 实例基于 baseOption 和media 绘制图表
* 通过对`window.resize`实践进行监听，在到达我们给`echarts`图标设置的窗口规则时，调用`echartInstance.resize()`方法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>响应式布局</title>
    <style>
        html,body{margin: 0;height: 100%}
        #main{
            height: 100%;
            background: antiquewhite;
        }
    </style>
</head>
<body>
<!--建立dom 容器-->
<div id="main"></div>
<!--引入echarts-->
<script src="../js/echarts.min.js"></script>
<script>
    /*实例化echarts*/
    const myChart = echarts.init(document.getElementById('main'));

    /*基础配置项 baseOption，建立两个绘图区
    *   grid 绘图区
    *   xAxis yAxis
    *       min 最小刻度值
    *       max 最大刻度值
    *       gridIndex 绘图区的索引位置
    *
    * */
    const baseOption={
        grid:[
            {left:'10%',right:'55%',top:'10%',bottom:'10%'},
            {left:'55%',right:'10%',top:'10%',bottom:'10%'},
        ],
        xAxis:[
            {min:0,max:50,gridIndex:0},
            {min:0,max:50,gridIndex:1},
        ],
        yAxis:[
            {min:0,max:50,gridIndex:0},
            {min:0,max:50,gridIndex:1},
        ],
    };

    /*media 规则配置列表
    *   query 规则，如maxWidth: 768
    *   option 配置项
    * */
    const media=[
        {
            option:{
                grid:[
                    {left:'10%',right:'55%',top:'10%',bottom:'10%'},
                    {left:'55%',right:'10%',top:'10%',bottom:'10%'},
                ],
            }
        },
        {
            query:{
                maxWidth:768
            },
            option:{
                grid:[
                    {left:'10%',right:'10%',top:'10%',bottom:'55%'},
                    {left:'10%',right:'10%',top:'55%',bottom:'10%'},
                ],
            }
        }

    ];
    
    /*绘图*/
    myChart.setOption({baseOption,media});

    /*窗口尺寸发生变化resize 时，echarts 实例使用resize() 方法重置尺寸*/
    window.addEventListener('resize',function(){
        myChart.resize();
    })

</script>
</body>
</html>
```

#### 3.9、主题

内置主题：

* ECharts 中默认内置了两套主题：`light`、`dark`

* 在初始化对象`init`中指明

  ```js
  const chart = ecahrts.init(dom,'light')
  const chart = ecahrts.init(don,'dark')
  ```

* 自定义主题

  在主题编译器中编辑主题

  下载主题，是一个js文件

  引入主题js文件

  在init方法中使用主题

#### 3.10、调色盘

调色盘：它是一组颜色，图形、系列会自动从其中选择颜色，它有

* 主题调色盘

* 全局调色盘

  ```js
  option:{
      color:['red','green','blue']
  }
  ```

* 局部调色盘

  ```js
  series:[{
      type:'bar',
      color:['red','green','blue']
  }]
  ```

**注意：调色盘的作用遵循就近原则**

颜色渐变:

* 线性渐变：

```js
itemStyle:{
    color:{
        type:'liner',
        x:0,
        y:0,
        x1:0,
        y1:0,
        colorStops:[{
            offset:0,color:'red' // 0%处的颜色
        },{
            offset:100%,color:'blur' // 100%处的颜色
        }]，
        globalCoord:false // 缺省为 false
    }
}
```

* 径向渐变

```js
itemStyle:{
    color:{
        type:'redial', 
        x:0.5,
        y:0.5,
        r:0.5,
        colorStops:[{
            offset:0,color:'red' // 0%处的颜色
        },{
            offset:100%,color:'blue' // 100%出颜色
        }]
        globalCoord:false // 缺省为false
    }
}
```

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="lib/echarts.min.js"></script>
    </head>

    <body>
        <div style="width: 600px;height:400px"></div>
        <script>
            var mCharts = echarts.init(document.querySelector("div"))
            var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
            var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
            var option = {
                xAxis: {
                    type: 'category',
                    data: xDataArr
                },
                yAxis: {
                    type: 'value'
                },
                series: [
                    {
                        type: 'bar',
                        data: yDataArr,
                        itemStyle: {
                            // color: {
                            //   type: 'linear', // 线性渐变
                            //   x: 0,
                            //   y: 0,
                            //   x2: 0,
                            //   y2: 1,
                            //   colorStops:[
                            //     {
                            //       offset: 0, color: 'red' // 0%处的颜色为红色
                            //     },
                            //     {
                            //       offset: 1, color: 'blue' // 100%处的颜色为蓝
                            //     }
                            //   ]
                            // }
                            color: {
                                type: 'radial', // 径向渐变
                                x: 0.5,
                                y: 0.5,
                                r: 0.5,
                                colorStops: [
                                    {
                                        offset: 0, color: 'red' // 0%处的颜色为红色
                                    },
                                    {
                                        offset: 1, color: 'blue' // 100%处的颜色为蓝
                                    }
                                ]
                            }
                        }
                    }
                ]
            }
            mCharts.setOption(option)
        </script>
    </body>

</html>
```

#### 3.11 样式

直接样式：itemStyle、textStyle 、lineStyle 、areaStyle 、label

高亮样式：在`emphasis`中包裹`itemStyle、textStyle、lineStyle、areaStyle、label`

它们的优先级高，会覆盖主题中、调色盘的效果

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
</head>


<body>
  <div style="width: 600px;height:400px"></div>
  <script>
    var mCharts = echarts.init(document.querySelector("div"))
    var option = {
      title: {
        text: '饼图的测试',
        textStyle: { // 控制标题的文字样式
          color: 'blue'
        }
      },
      series: [
        {
          type: 'pie',

          data: [{
            value: 11231,
            name: "淘宝",
            itemStyle: { // 控制淘宝这一区域的样式
              color: 'yellow'
            },
            label: {
              color: 'green'
            },
            emphasis: {
              itemStyle: { // 控制淘宝这一区域的样式
                color: 'pink'
              },
              label: {
                color: 'black'
              }
            }
          },
          {
            value: 22673,
            name: "京东"
          },
          {
            value: 6123,
            name: "唯品会",
          },
          {
            value: 8989,
            name: "1号店"
          },
          {
            value: 6700,
            name: "聚美优品"
          }]
        }
      ]
    }
    mCharts.setOption(option)
  </script>
</body>

</html>
```



### 四、交互API

echarts中交互API，主要挂载两个地方，一个是 全局`echarts`对象，另一个是通过`echarts.init()`创建的实例对象：`echartsInstance`

#### 4.1、全局 echarts 对象

全局对象下的方法有：

* init  ：初始化Echarts 实例对象，使用主题
* registerTheme ：注册主题，只有注册过的主题，才能在init方法中使用该主题
* registerMap ：注册地图数据

```js
// 注册地图数据
$.get('json/map/china.json',function(chinaJson) {
    echarts.registerMap('china',chinaJson)
})
// geo组件使用地图数据
const option = {
    geo:{
        type:'map',
        map:'china'
    }
}
```

* connect :

一个页面中可以有多个独立的图标，每个图表对于一个Echarts独立的实例对象。而connect可以实现多图关联，它传入联动目标为`Echarts`实例对象，支持数组。这个功能主要用于`保存图片的自动拼接、刷新按钮、重置按钮，提示框联动、图例选择、数据范围修改等....业务需求上`

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <script src="lib/echarts.min.js"></script>
  <script src="theme/itcast.js"></script>
  <script src="lib/jquery.min.js"></script>
</head>

<body>
  <div style="width: 600px;height:400px;border: 1px solid red"></div>
  <div style="width: 600px;height:400px;border: 1px solid green" id="div1"></div>
  <script>
    var mCharts = echarts.init(document.querySelector("div"), 'itcast')
    var xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
    var yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]
    var option = {
      toolbox: {
        feature: {
          saveAsImage: {}
        }
      },
      xAxis: {
        type: 'category',
        data: xDataArr
      },
      yAxis: {
        type: 'value'
      },
      series: [
        {
          type: 'bar',
          data: yDataArr,
          markPoint: {
            data: [
              {
                type: 'max', name: '最大值'
              },
              {
                type: 'min', name: '最小值'
              }
            ]
          },
          markLine: {
            data: [
              {
                type: 'average', name: '平均值'
              }
            ]
          },
          label: {
            show: true,
            rotate: 60
          },
          barWidth: '30%'
        }
      ]
    }
    mCharts.setOption(option)

    var mCharts2 = echarts.init(document.querySelector('#div1'))
    $.get('json/map/china.json', function(ret){
      echarts.registerMap('aa', ret)
      var option2 = {
        geo: {
          type: 'map',
          map: 'aa'
        }
      }
      mCharts2.setOption(option2)
      echarts.connect([mCharts, mCharts2]) // 将柱状图和地图关联起来
    })
  </script>
</body>

</html>
```

#### 4.2、实例对象

Echarts 实例对象下有：

* setOption：

设置或修改图标实例的配置以及数据，多次调用`setOptiobn`方法，作用是：合并新的配置和旧的配置，做增量动画

* resize

重新算和绘制图表，一般和`window`对象的`resize`事件结合使用

```js
window.onresize = function() {
    mChart.resize()
}
```

* on \ off

绑定或解绑事件处理函数

鼠标事件：

​	常见事件：`click`、`dblclick`、`mousedown`、`mousemove`、`mouseup`等

​	事件参数`arg`：它包含了事件相关的数据信息

Echarts 事件

​	常见的事件`legendselectedchanged`、`datazoom`、`pieselectchanged`、`mapselectchanged`等

​	事件参数`arg`：包含事件相关的数据信息

* dispatchAction

触发某些行为，使用代码模拟用户的行为

```js
mCharts.dispatchAction({
    type:'highlight', // 事件类型
    seriesIndex:0, // 图表索引
    dataIndex:1 // 图表中哪一项高亮
})
```

* clear

清空当前实例，会移除实例中所有的组件和图表，但清空之后，我们可以再次进行`mcharts.setOption(option)`对图表进行重新绘制

* dispose

销毁实例，销毁之后实例无法在被使用，也就是无法在`mChasrts.setOption(option)`进行重新绘制

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
        <script src="lib/echarts.min.js"></script>
        <script src="lib/jquery.min.js"></script>
    </head>

    <body>
        <div style="width: 600px;height:400px"></div>
        <button id="btn1">触发行为</button>
        <button id="btn2">clear</button>
        <button id="btn3">setOption</button>
        <button id="btn4">dispose</button>
        <script>
            var mCharts = echarts.init(document.querySelector("div"))
            var pieData = [
                {
                    value: 11231,
                    name: "淘宝",
                },
                {
                    value: 22673,
                    name: "京东"
                },
                {
                    value: 6123,
                    name: "唯品会"
                },
                {
                    value: 8989,
                    name: "1号店"
                },
                {
                    value: 6700,
                    name: "聚美优品"
                }
            ]
            var option = {
                legend: {
                    data: ['淘宝', '京东', '唯品会', '1号店', '聚美优品']
                },
                tooltip: {
                    show: true
                },
                series: [
                    {
                        type: 'pie',
                        data: pieData
                    }
                ]
            }
            mCharts.setOption(option)
            mCharts.on('click', function (arg) {
                console.log(arg)
                console.log('click...')
            }) // 对事件进行监听

            mCharts.off('click') // 解绑click的事件

            mCharts.on('legendselectchanged', function (arg) {
                console.log(arg)
                console.log('legendselectchanged')
            })

            $('#btn1').click(function () {
                // 模拟用户的行为
                mCharts.dispatchAction({
                    type: 'highlight',
                    seriesIndex: 0, // 系列的索引
                    dataIndex: 1 // 数据的索引
                })
                mCharts.dispatchAction({
                    type: 'showTip',
                    seriesIndex: 0,
                    dataIndex: 2
                })
            })

            $('#btn2').click(function () {
                // 清空图表的实例
                mCharts.clear()
            })

            $('#btn3').click(function () {
                // 重新设置option
                mCharts.setOption(option)
            })

            $('#btn4').click(function () {
                // 销毁mCharts
                mCharts.dispose()
            })
        </script>
    </body>

</html>
```

