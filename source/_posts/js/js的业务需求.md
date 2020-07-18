---
title: js的业务需求
date: 2020-07-09 09:32:32
categories: JS
top: false
summary: js的业务需求
tags: 
 - JS
---

## JS的业务需求解决

## 一、将数值格式化为`万`、`亿`等

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

