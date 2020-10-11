---
title: 小程序HTTPAPI
date: 2020-09-29 12:12:08
categories: 小程序
top: false
summary: 小程序云开发
tags: 
 - 小程序
---

### 小程序的HTTPAPI调用

我们小程序能够采用云开发的模式，它在云端上就构建有我们小程序的云数据库。这时我们想要构建一个后台管理系统来操作该云数据库，我们可以使用小程序给我们提供的`HTTPAPI`来触发我们的云函数、操作我们云数据库。

![HTTP模式前后端](/medias/imges/xcx/httpApi/xcx_HTTPAPI_1.png)

### 一、获取token

获取小程序全局唯一后台接口调用凭据（`access_token`）。**调用绝大多数后台接口时都需使用 access_token，开发者需要进行妥善保存。**`access_token` 的有效期目前为 **2 个小时**，需定时刷新，重复获取将导致上次获取的 `access_token` 失效；

定时更新的token的node代码：它会在比较上一次获取的token是否超过两个小时，若超过就重新获取并更新token，反之，直接输出token

```js
// 用户发送请求的包
const rp = require('request-promise')
const APPID = 'wxc1c91e54f209bdc4'
const APPSECRET = '7ff2961fd6964ea372f48ddb78bd06f5'
const URL = `https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=${APPID}&secret=${APPSECRET}`
const fs = require('fs')
const path = require('path')
const fileName = path.resolve(__dirname, './access_token.json')

const updateAccessToken = async () => {
    // 发送请求
    const resStr = await rp(URL)
    const res = JSON.parse(resStr)
    console.log(res)
    // 写文件
    if (res.access_token) {
        fs.writeFileSync(fileName, JSON.stringify({
            access_token: res.access_token,
            createTime: new Date()
        }))
    } else {
        // 未获取token时，再一次获取
        await updateAccessToken()
    }
}

const getAccessToken = async () => {
    // 读取文件
    try {
        const readRes = fs.readFileSync(fileName, 'utf8')
        const readObj = JSON.parse(readRes)
        const createTime = new Date(readObj.createTime).getTime()
        const nowTime = new Date().getTime()
        // 当服务器荡机时更新
        if ((nowTime - createTime) / 1000 / 60 / 60 >= 2) {
            await updateAccessToken()
            await getAccessToken()
        }
        return readObj.access_token
    } catch (error) {
        // 没有该 access_token 文件时更新
        await updateAccessToken()
        await getAccessToken()
    }
}

setInterval(async () => {
    await updateAccessToken()
}, (7200 - 300) * 1000)

// updateAccessToken()
// console.log(getAccessToken())
module.exports = getAccessToken
```

### 二、触发云函数

我们云函数可以通过外部触发，这里我们将它封装成一个方法

详细看[官方文档](https://developers.weixin.qq.com/miniprogram/dev/wxcloud/reference-http-api/functions/invokeCloudFunction.html)

```js
const getAccessToken = require('./getAccessToken.js')
const rp = require('request-promise')

const callCloudFn = async (ctx, fnName, params) => {
    // 得到上一步的token值
    const ACCESS_TOKEN = await getAccessToken()
    const options = {
        method: 'POST',
        uri: `https://api.weixin.qq.com/tcb/invokecloudfunction?access_token=${ACCESS_TOKEN}&env=${ctx.state.env}&name=${fnName}`,
        body: {
            // 接口需要的参数
            ...params
        },
        json: true // Automatically stringifies the body to JSON
    }

    return await rp(options)
        .then((res) => {
            return res
        })
        .catch(function (err) {
        })
}

module.exports = callCloudFn
```

### 三、操作云数据库

我们这里也是将其封装成一个方法：

[详细请看官网文档](https://developers.weixin.qq.com/miniprogram/dev/wxcloud/reference-http-api/database/)

```js
const getAccessToken = require('./getAccessToken.js')
const rp = require('request-promise')

const callCloudDB = async(ctx, fnName, query = {}) => {
    const ACCESS_TOKEN = await getAccessToken()
    const options = {
        method: 'POST',
        uri: `https://api.weixin.qq.com/tcb/${fnName}?access_token=${ACCESS_TOKEN}`,
        body: {
            query,
            env: ctx.state.env,
        },
        json: true // Automatically stringifies the body to JSON
    }

    return await rp(options)
        .then((res) => {
            return res
        })
        .catch(function (err) {
            console.log(err);
        })

}

module.exports = callCloudDB
```

### 四、操作云存储

对于云存储，我们要有特殊的操作，例如：图片形式。对于图片形式，我们在小程序存储的是`cloud://`协议的开头，这只能在我们小程序上将其显示，它并不能作为我们我也的请求路径，这时我们就要先对其进行转发，使用获取下载路径这个api来对其进行处理成我们要的https协议开头的请求路径。而我们要上传一张图片时，我们就要将其转换为`cloud://`协议开发的路径，后在将其转发到我们云存储上。

```js
const getAccessToken = require('./getAccessToken.js')
const rp = require('request-promise')
const fs = require('fs')

const cloudStorage = {
    // 获取下载链接
    async download(ctx, fileList) {
        const ACCESS_TOKEN = await getAccessToken()
        const options = {
            method: 'POST',
            uri: `https://api.weixin.qq.com/tcb/batchdownloadfile?access_token=${ACCESS_TOKEN}`,
            body: {
                env: ctx.state.env,
                file_list: fileList
            },
            json: true
        }

        return await rp(options)
            .then((res) => {
                return res
            })
            .catch(function(err) {
                console.log(err);
            })

    },
	// 上传图片
    async upload(ctx) {
        // 1、请求地址
        const ACCESS_TOKEN = await getAccessToken()
        const file = ctx.request.files.file
        const path = `swiper/${Date.now()}-${Math.random()}-${file.name}`
        const options = {
            method: 'POST',
            uri: `https://api.weixin.qq.com/tcb/uploadfile?access_token=${ACCESS_TOKEN}`,
            body: {
                path,
                env: ctx.state.env,
            },
            json: true // Automatically stringifies the body to JSON
        };
        //  请求参数的
        const info = await rp(options)
            .then(function(res) {
                return res
            })
            .catch(function(err) {})
        console.log(info)
            // 2、上传图片
        const params = {
            method: 'POST',
            headers: {
                'content-type': 'multipart/form-data'
            },
            uri: info.url,
            formData: {
                key: path,
                Signature: info.authorization,
                'x-cos-security-token': info.token,
                'x-cos-meta-fileid': info.cos_file_id,
                file: fs.createReadStream(file.path)
            },
            json: true
        }
        await rp(params)
        return info.file_id
    },

    async delete(ctx, fileid_list) {
        const ACCESS_TOKEN = await getAccessToken()
        const options = {
            method: 'POST',
            uri: `https://api.weixin.qq.com/tcb/batchdeletefile?access_token=${ACCESS_TOKEN}`,
            body: {
                env: ctx.state.env,
                fileid_list: fileid_list
            },
            json: true
        }

        return await rp(options)
            .then((res) => {
                return res
            })
            .catch(function(err) {
                console.log(err);
            })
    }
}

module.exports = cloudStorage
```

