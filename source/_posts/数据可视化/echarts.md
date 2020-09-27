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
* 位置 ：left：let (左对齐)|right(右对齐)|center(矩阵)
* 主标题样式：textStyle ：{} 【官方查阅】
* 副标题样式：subtextStyle ：{}
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

图例相当于是不同系列的数据过滤，它需要与`series`中的`name属性`搭配使用

```js
// ...
legend:{
    data:['难度','人数']
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

提示框触发方式 `trigger`,

* item 图形触发，主要在散点图，饼图等无类目的图表中使用
* axis 坐标轴触发，主要在柱状图，折线图，等会使用类目轴的图表
* none 什么都不触发

```js
// ...
tooltip:{
    trigger:'axis'
},
// ...
```

#### 1.5 坐标轴 xAxis:{} |  yAxis{}

* xAixs
  * name 坐标轴名称
  * data 类目数据
* yAixs
  * splitNumber 分割段数
  * interval 强制设置坐标轴分割间隔
  * minInterval 坐标轴最小间隔
  * maxInterval 坐标轴最大间隔

```js
// ...
xAxis:{
    name:'前端语言类型',
        data:['html','css','js']
},

yAxis:{
    name:'人数',
    splitNumber:9,
   	interval:5,
    minInterval: 20,
    maxInterval: 5,
}
// ...
```

#### 1.6 系列列表 series

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

#### 注意：

要画多个坐标系时，xAxis，yAxis要写成对象的形式，并且要将`grid`写成对应多个，并且在同一个坐标系下的x，y轴要使用同一个`girdIndex`，而在`series`系列下，使用`xAxisIndex、yAxisIndex`来分别指定该系列应该画在哪个x，y轴。这样在series系列中就不用写`gridIndex`对应的绘图区域，因为x，y轴上就已经规定了。

dimensions 维度，如果不指定这是数据源dataset的第一列或第一行为维度名。维度名是不显示在坐标轴上的，他显示在图例的位置上

### 二、echarts 常用图表

echarts中常用的图标我们有：折线图`line`、饼图`pie`、散点图`scatter`、K线`candlestick`、雷达`radar`、仪表盘`gauge`、地图`map`

#### 2.1 折线图用法

折线图主要用来展示数据相随着时间推移的变化，折线图非常适用于展示一个连续的二维数据，如某网站访问人数或商品销量价格的波动

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

                /*y轴*/
                yAxis: {
                    axisLabel: {
                        margin: 18
                    }
                },
                /*series 系列集合
             *   type 系列类型，line
             *   name 系列名
             *   data 系列数据，[20,10,30,40]
             *   smooth 平滑
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

#### 2.2 饼图

饼图主要用于展现不同类别数值相对于总数的占比情况，图中的扇形的弧长表示该类别的占比大小，所有扇形的弧长的总合为100%，当各类别数据占比较接近时，建议选中柱状图或南丁格尔玫瑰图

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>饼图</title>
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
            // 基于准备好的dom，初始化echarts实例
            const myChart = echarts.init(document.getElementById('main'));
            const data = [{
                name: 'css',
                value: 20
            }, {
                name: 'html',
                value: 30
            }, {
                name: 'js',
                value: 40
            }, {
                name: 'canvas',
                value: 50
            }, ];
            // 指定图表的配置项和数据
            const option = {
                /*视觉映射 visualMap
             *   false 可见性
             *   min 最小值
             *   max 最大值
             *   inRange 定义 在选中范围中 的视觉元素
             *       colorLightness[0, 1] 亮度
             * */
                visualMap: {
                    min: data[0].value,
                    max: data[data.length - 1].value,
                    inRange: {
                        colorLightness: [0.3, 0.8]
                    },
                    show: false,
                },

                /*饼图 pie
             *   type 图表类型
             *   data 数据 [{name,value},...]
             *   roseType 玫瑰图类型
             *       radius 半径
             *       area 面积
             *   radius 半径，[起始半径，结束半径]可生成环形
             *   itemStyle 项目样式
             *       color 颜色
             * */
                series: {
                    type: 'pie',
                    data,
                    roseType: 'radius',
                    itemStyle: {
                        color: 'red'
                    }
                }

            };
            // 使用刚指定的配置项和数据显示图表。
            myChart.setOption(option);
        </script>
    </body>

</html>
```

#### 2.3 散点图

散点图通常用来识别两个变量之间的相关性或用来观察他们的关系，从而发现某种趋势，对于查找异常值或理解数据分布也很有效。散点图可以将一个对象的两个变量映射到 x、y位置上。如果此对象还要一个变量，那就可以映射到散点的大小上。

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

#### 2.4 k线图

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

#### 2.5 雷达图用法

雷达图的每个变量都有一个从中心向外发射的轴线，所有的轴之间的夹角相等，同时每个轴有相同的刻度。雷达图表适合对比变量在数据集内的高低，比如产品性能、排名、评估等。

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

#### 2.6 仪表盘的用法

仪表盘适合表示量的变化，如速度、体积、温度、进度、完成率、满意度等

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

#### 2.7 地图可视化

地图主要用于地理区域数据的可视化

1. 下载地图文件：http://datav.aliyun.com/tools/atlas/#&lat=31.80289258670676&lng=104.2822265625&zoom=4
2. 注册地图：echarts.registerMap('china',data)
3. 配置地图

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

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>中国地图</title>
        <style>
            body{margin: 0}
            #main{
                width: 100%;
                height: 600px;
                background-color: #044161;
            }
        </style>
    </head>
    <body>
        <!--建立dom 容器-->
        <div id="main"></div>
        <!--引入echarts-->
        <script src="../js/echarts.min.js"></script>

        <script>
            const myChart = echarts.init(document.getElementById('main'));
            /*获取接送文件*/
            fetch('../js/China.json')
                .then((res) => res.json())
                .then(data => {
                echarts.registerMap('china',data);
                const option = {
                    title: {
                        text: '中国地图',
                        left:'center',
                        textStyle:{
                            color:'rgba(255,255,255,0.8)',
                        },
                        top:24
                    },
                    geo: {
                        map: 'china',
                        // 是否可鼠标缩放，鼠标滑动
                        roam:true,
                        zoom:1,
                        itemStyle:{
                            areaColor:'#004981',
                            borderColor:'#029fd4'
                        },
                        // 高亮状态下的多边形和标签样式
                        emphasis:{
                            itemStyle:{
                                color:'#029fd4'
                            },
                            label:{
                                color:'#fff'
                            }
                        }
                    },
                    series: [{
                        name: 'pm2.5',
                        type: 'scatter',
                        // 该系列使用的坐标系 地图也是一个坐标系，可叫地图坐标系
                        coordinateSystem:'geo',
                        data: [
                            {
                                name:'海门',
                                value:[121.15, 31.89, 9]
                            },
                            {
                                name:'鄂尔多斯',
                                value:[109.781327, 39.608266, 12]
                            },
                            {
                                name:'招远',
                                value:[120.38, 37.35, 30]
                            },

                        ],
                        symbolSize: function (val) {
                            return val[2];
                        },
                    }]
                };
                myChart.setOption(option);
            })

        </script>
    </body>
</html>

```

#### 2.8 实例：疫情折线图

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

#### 3.2 数据更新

对于请求数据的方式，ajax、fetch 都可以，而在echarts 中数据的更新有两种思路

* 请求到数据后，在绘制（echarts实例对象.setOption()）
* 先绘制（echarts实例对象.setOption()），有什么先配置什么。请求数据之后在追加其他数据配置，能这么做原因是 echarts 内置会为我们保留上一次操作的状态，可以理解为我们下一步的操作其实是去覆盖上一次的状态。

注意：在数据加载的过程中，我们还可以使用 echarts 内置的 loading组件来提示用户

* 显示loading ： echarts实例化对象.showLoading()
* 隐藏loading：echarts实例对象.hideLoading()

```html
<!DOCTYPE html>
<html lang="en">

    <head>
        <meta charset="UTF-8">
        <title>异步数据</title>
        <style>
            #main {
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
            const myChart = echarts.init(document.getElementById('main'));
            // 先有什么就绘制什么
            myChart.setOption({
                title: {
                    text: '中国地图',
                    left: 'center'
                }
            });

            /*显示loading - showLoading()*/
            myChart.showLoading();

            /*异步请求数据*/
            fetch('../js/China.json')
                .then((res) => res.json())
                .then(data => {
                echarts.registerMap('china', data);
                // 追加配置
                myChart.setOption({
                    series: {
                        type: 'map',
                        map: 'china'
                    }
                });

                /*隐藏loading - hideLoading()*/
                setTimeout(function() {
                    myChart.hideLoading();
                }, 1000)
            })
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

#### 3.4区域缩放 dataZoom

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
    <title>dataZoom</title>
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

    //数据源
    const source = [
        //x   y   z
        [2,  1, 10],
        [4,  2, 20],
        [6,  3, 30],
        [8,  4, 50],
        [10, 5, 50],
        [12, 6, 60],
        [14, 7, 70],
        [16, 8, 80],
        [18, 9, 90],
    ];

    // 指定图表的配置项和数据
    const option = {
        tooltip: {},
        /*工具栏 toolbox
        *   feature{} 工具配置项
        *     dataZoom{} 框选型缩放
        * */
        toolbox:{
            feature:{
                dataZoom:{}
            }
        },

        /*
        * x 轴
        *   min 最小值
        *       dataMin 取所有数据中的最小值
        *   max 最大值
        *       dataMax 取所有数据中的最大值
        * */
        xAxis: {
            type: 'value',
            min: 'dataMin',
            max: 'dataMax',
        },
        yAxis: {
            type: 'value',
            min: 'dataMin',
            max: 'dataMax',
        },
        /*
        * dataZoom 区域缩放 [{},{}]
        *   type 缩放方式
        *       inside 内置缩放，通过鼠标的平移缩放实现
        *       slider 滑动条缩放
        *   xAxisIndex 设置 dataZoom-inside 组件控制的 x轴
        *       [n] 控制xAxis 中的索引位置为n 的轴
        *   start 起始位，百分百 [0,100]
        *   end 结束位，百分百 [0,100]
        * */
        dataZoom:[
            {
                type:'inside',
                // xAxisIndex:[0],
                start:0,
                end:100
            },
            /*{
                type:'slider',
                start:0,
                end:100
            },*/
        ],

        /*数据集*/
        dataset:{source},
        /*系列列表*/
        series: [
            {
                name: '系列 1',
                type: 'scatter',
                itemStyle: {
                    opacity: 0.7
                },
                symbolSize: function (val) {
                    return val[2]/2;
                },

            },
        ]
    };


    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);
</script>
</body>
</html>
```

#### 3.5 visualMap 视觉映射的原理

visualMap 视觉映射可以让项目的数据和项目的颜色、大小等属性相关联。例如;

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

### 四、echarts的事件监听

#### 4.1 鼠标事件监听

Echarts 使用 `on`绑定事件，事件名称对应DOM 事件名称，均为小写的字符串，如：

```js
// myChart 是 echarts.init() 实例化出来的对象
myChart.on('click',function(params){
    // 控制台打印数据的名称
    console.log(params.name)
})
```

鼠标事件有哪些：Echarts 支持常规的鼠标事件类型，包括：`click`、`dblclick`、`mousedown`、`mouseup`、`mouseover`、`mouseout`、`globalout`、`contextmenu`事件

所有的鼠标事件包含参数 `params`,如被点击的图形信息 params.componentType

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>事件</title>
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
        xAxis: {
            data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
        },
        yAxis: {},
        series: [{
            name: '销量',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
        }]
    };
    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);

    /*
    * 使用on 方法绑定click点击事件
    * */
    myChart.on('click',function(param){
        console.log(param)
    })
</script>
</body>
</html>
```

#### 4.2 组件交互事件监听

在Echarts 中基本上所有的组件交互行为都会触发相对应的事件，常用的事件和事件对应参数在[events](https://echarts.apache.org/zh/api.html#events)

文档中有列出，如

```js
// 图例开关的行为会触发 legendselectchanged 事件
myChart.on('legendselectchanged',function(params){
    // 获取点击图例的选中状态
    let isSelected = params.selected[params.name];
    // 控制台中打印
    console.log(isSelected?'选中了':'取消选中了')
    // 打印所有图例的状态
    console.log(params.selected)
})
```

实例：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>事件</title>
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
        // 基于准备好的dom，初始化echarts实例
        const myChart = echarts.init(document.getElementById('main'));

        // 指定图表的配置项和数据
        const option = {
            legend: {
                data: ['销量', '库存']
            },
            xAxis: {
                data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
            },
            yAxis: {},
            series: [{
                name: '销量',
                type: 'bar',
                data: [5, 20, 36, 10, 10, 20]
            }, {
                name: '库存',
                type: 'bar',
                data: [3, 4, 8, 3, 7, 5]
            }]
        };
        // 使用刚指定的配置项和数据显示图表。
        myChart.setOption(option);
        
        /*
         * 使用on 方法绑定legendselectchanged 图例开关点击事件
         * */
        myChart.on('legendselectchanged', function(param) {
            console.log(param)
        })
    </script>
</body>
</html>
```

#### 4.3 触发Echarts 中组件行为

Echarts 通过调用`myChart.dispatchAction({type:''})`触发图表行为，如

```js
myChart.dispatchAction({
    type:'highlight',
    seriesIndex:0,
    dataIndex:app.currentInex
})
```

实例：每秒切换饼图的提示信息

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>事件</title>
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
        title: {
            text: '饼图程序调用高亮示例',
            left: 'center'
        },
        tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        legend: {
            orient: 'vertical',
            left: 'left',
            data: ['直接访问', '邮件营销', '联盟广告', '视频广告', '搜索引擎']
        },
        series: {
            name: '访问来源',
            type: 'pie',
            radius: '55%',
            center: ['50%', '60%'],
            data: [
                {value: 335, name: '直接访问'},
                {value: 310, name: '邮件营销'},
                {value: 234, name: '联盟广告'},
                {value: 135, name: '视频广告'},
                {value: 1548, name: '搜索引擎'}
            ],
            /*鼠标划上的状态*/
            emphasis: {
                itemStyle: {
                    shadowBlur: 10,
                    shadowOffsetX: 0,
                    shadowOffsetY: 10,
                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                }
            }
        }
    };
    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);

    /*当前索引*/
    let ind=0;

    /*获取系列数据的长度*/
    let len =5;

    /*使用dispatchAction 方法高亮并提示一个扇形
    *   type 触发的行为类型
    *       highlight 高亮
    *       showTip 显示提示
    *       downplay 取消高亮
    *       hideTip 取消提示
    *   seriesIndex 系列索引，用于寻找系列列表中的某个系列
    *   dataIndex 数据所有，用于寻找系列中的某个元素
    * */
    myChart.dispatchAction({
        type:'highlight',
        seriesIndex:0,
        dataIndex:ind
    });
    myChart.dispatchAction({
        type:'showTip',
        seriesIndex:0,
        dataIndex:ind
    });

    /*建立时间监听器*/
    setInterval(function(){
        myChart.dispatchAction({
            type:'hideTip',
            seriesIndex:0,
            dataIndex:ind
        });
        myChart.dispatchAction({
            type:'downplay',
            seriesIndex:0,
            dataIndex:ind
        });

        ind++;
        if(ind===len){ind=0}
        myChart.dispatchAction({
            type:'highlight',
            seriesIndex:0,
            dataIndex:ind
        });
        myChart.dispatchAction({
            type:'showTip',
            seriesIndex:0,
            dataIndex:ind
        });

    },1000)
</script>
</body>
</html>
```

