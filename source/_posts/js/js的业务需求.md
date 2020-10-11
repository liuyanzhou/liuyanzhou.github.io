---
title: js的业务需求
date: 2020-07-09 09:32:32
categories: JS
top: false
summary: js的业务需求
tags: 
 - JS
---

### JS的业务需求解决

### 一、将数值格式化为`万`、`亿`等

```js
/** 
	num 传入的数值
	point 是要保留多少小数位
	例如 statisticsNum(1464123.8,2) -> 146.41 万
*/
statisticsNum(num, point) {
    let numStr = num.toString().split('.')[0]
    if (numStr.length < 6) {
        return numStr
    } else if (numStr.length >= 6 && numStr.length <= 8) {
        let decimal = numStr.substring(numStr.length - 4, numStr.length - 4 + point)
        return parseFloat(parseInt(num / 10000) + '.' + decimal) +
            '万'
    } else if (numStr.length > 8) {
        let decimal = numStr.substring(numStr.length - 8, numStr.length - 8 + point)
        return parseFloat(parseInt(num / 100000000) + '.' + decimal) + '亿'
    }
}
```

### 二、图片与base64

#### 2.1、将图片对象转化为base64

* 使用 `FileReader`对象的 `readAsDataURL`方法来进行转化

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <input type="file" name="" id="fileInput">
</body>
<script>
    let fileInput = document.querySelector('#fileInput')
    fileInput.addEventListener('change', function(e) {
        const [file] = e.target.files
            // 转化为base64
        let reader = new FileReader()
        reader.addEventListener('load', function(e) {
            // 得到图片对象转为base64格式数据
            const base64Image = e.target.result;
            // 这里我们可以通过创建img标签，通过base64Image格式数据放入 img的src上，在通过appendChild添加到页面上显示
            console.log(base64Image)
        })
        reader.readAsDataURL(file)
    })
</script>
</html>
```

* 使用`canvas`来对图片进行转化

使用canvas 那必须先把图像画到`canvas`上，在通过`canvas.toDataURL`进行读取转化，在使用`canvas.toDateURL(type,[encoderOptions])`，需要注意：

* type：是需要输出的图片格式，默认是`image/png`
* encoderOptions：图片质量，**只有到`type`为`image/jpeg`或`image/webp`格式时，这个参数才起效**，参数范围为`0~1`，超过这个范围取`0.92`,慎用`1`，很有可能出现比图片还大的导出图片，一般小编设为`0.5`

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
</body>
<script>
    let image = new Image();
    image.addEventListener('load', function(e) {
        const canvas = document.createElement('canvas')
        canvas.width = image.naturalWidth
        canvas.height = image.naturalHeight
        const ctx = canvas.getContext('2d')
        ctx.clearRect(0, 0, canvas.width, canvas.height)
        ctx.drawImage(image, 0, 0, canvas.width, canvas.height)
        document.body.appendChild(canvas)
        // 得到canvas上的图片base64格式
        const base64Image = canvas.toDataURL('image/jpeg')
        console.log(base64Image)
    })
    // 读取的图片路径
    image.src = './one.jpeg'
    const canvas = document.createElement('canvas')
</script>

</html>
```

#### 2.2、base64图片转化为图片对象格式

```js
// dataUrl: 传入base64格式的图片
// filename: 转换后的图片名称 
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
```

#### 2.3前端本地下载base64格式的图片

本地下载`base64`图片，我们可以通过`a`链接的功能来实现，步骤为：

* 创建`a`标签，将`base64`格式传入`a的href`属性
* 添加`a`标签的`download=xxx`属性，这里的xxx是下载后的文件名称，需要带文件名后缀
* 模拟 点击`a`连接，例如:`aLink.click()`

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
</body>
<script>
    let image = new Image();
    image.addEventListener('load', function(e) {
        const canvas = document.createElement('canvas')
        const aLink = document.createElement('a')
        canvas.width = image.naturalWidth
        canvas.height = image.naturalHeight
        const ctx = canvas.getContext('2d')
        ctx.clearRect(0, 0, canvas.width, canvas.height)
        ctx.drawImage(image, 0, 0, canvas.width, canvas.height)
        document.body.appendChild(canvas)
        // 得到canvas上的图片base64格式
        const base64Image = canvas.toDataURL('image/jpeg', 0.1)
        aLink.href = base64Image
        aLink.download = '1.jpg'
        aLink.click()
    })
    image.src = './one.jpeg'
    const canvas = document.createElement('canvas')
</script>

</html>
```





