---
title: Ajax的三种写法
tags:
  - 前端
  - JavaScript
  - 原生JavaScript
  - jQuery
  - Node.js
  - axios
  - Ajax
categories: 前端技术
abbrlink: 3562520477
date: 2020-07-07
---

> **摘要：** 本篇文章总结并对比了使用原生JavaScript、jQuery和Node.js中的axios库通过Ajax技术请求后端接口时的不同写法。

# 前言

研究前端开发也有不少日子了，算起来练习时长怎么也得有两年半了吧，一直没怎么认真做过总结。再加上这个博客自从搭建好了之后就没怎么用过，所以今天先来总结一下自己平常用过的几种Ajax请求的编写方法，同时也给这个博客增加点内容。

代码风格是典型的Java风格，习惯其他风格的同学就凑合看吧！

当然，这里还需要注意的一点是，本篇博客中所涉及到的代码都只是请求的发送，不包括对跨域问题的解决。有关跨域问题的解决过些日子我会单独写一篇文章来介绍。~~才，才不是因为我还没完全学懂呢，哼！~~

此外，由于同步Ajax请求实际用的不是很多，本篇博客中涉及到的所有请求均为异步请求。如果各位同学有将请求同步化的需求，还请自行到相关的网站上进行查询，或查阅官方文档。在文章的合适位置我也会放置一些相关链接供同学们查看。

限于篇幅，这里只介绍最常用的两种请求方式：GET和POST。HTTP中定义的其他几种请求方式的写法大都与之类似，感兴趣或者有需求的同学们可以自行查找相关资料进行学习。

（*Ps: 虽然程序员平常干的也是码字的活，但是写博客总结知识是真的花时间……*）

> 前排提示：如果代码中有错误欢迎在评论区指出，我会及时更正。

# Ajax简介

~~JavaScript发送Ajax请求是怎么回事呢？JavaScript相信大家都很熟悉，但是JavaScript发送Ajax请求是怎么回事呢，下面就让小编带大家一起了解吧。~~

根据W3school的介绍，Ajax是一种在无需重新加载整个网页的情况下，通过在后台与服务器进行少量的数据交换，从而实现对网页中的某个部分进行异步更新的技术。不过我估计能看到这篇文章的同学大都对Ajax技术的意义以及优缺点有或多或少的了解，所以我在这里就不再赘述了。

# GET请求

## 1.  接口文档

假设有如下接口：

- **功能：** 获取新的推荐列表，用于在首页进行展示

- **地址：** `https://baseurl/common/push/homepage`

- **请求方式：** GET

- **请求头：** 

| 参数名       | 是否必须 | 类型   | 说明                       |
| ------------ | -------- | ------ | -------------------------- |
| Content-Type | 是       | string | 请求类型：application/json |

- **请求参数：** 

| 参数名   | 是否必须 | 类型   | 说明                       |
| -------- | -------- | ------ | -------------------------- |
| listSize | 否       | Number | 要获取的列表大小，默认为10 |

- **返回示例：**

正确时返回：

```json
{
    "code": 0,
    "msg": "请求成功",
    "data": {
        "pushList": [
            {"id": 1, "name": "请求示例01", "content": "请求示例01"},
            {"id": 2, "name": "请求示例02", "content": "请求示例02"},
            // 省略……
            {"id": 10, "name": "请求示例10", "content": "请求示例10"}
        ]
    }
}
```

错误时返回：

```json
{
    "code": 3011,
    "msg": "获取列表失败"
}
```

- **返回参数说明：** 

| 参数名                | 类型   | 说明                           |
| --------------------- | ------ | ------------------------------ |
| code                  | Number | 状态代码，请求成功则为0        |
| msg                   | String | 请求状态，作为对请求代码的说明 |
| data                  | Object | 返回值，仅当请求成功时存在     |
| data.pushList         | Array  | 推送列表                       |
| data.pushList.id      | Number | 推送内容的ID                   |
| data.pushList.name    | String | 推送内容的标题                 |
| data.pushList.content | String | 推送内容的简介                 |

## 2.  原生JavaScript

说来惭愧，虽然我前端代码也写了不少，其实原生的JavaScript我也不咋会写，甚至没做过浏览器兼容性上的优化。我一直都是套用的`菜鸟教程`上的所谓“模板”代码……

有兴趣的同学可以移步至[菜鸟教程 - AJAX实例](https://www.runoob.com/ajax/ajax-example.html)，看看我当初到底都学了点儿啥。

接下来放一段示例代码：

```javascript
// 首先根据浏览器的不同创建xmlhttp对象
var xmlhttp;
if (window.XMLHttpRequest) {
    xmlhttp = new XMLHttpRequest(); // IE7+, Firefox, Chrome, Opera, Safari
} else {
    xmlhttp = new ActiveXObject("Microsoft.XMLHTTP"); // IE6, IE5
}
// 为xmlhttp对象绑定响应函数，该函数每当请求状态改变时就会被触发一次
xmlhttp.onreadystatechange = function() {
    if (xmlhttp.readyState === 4 && xmlhttp.status === 200) {
        // 此时访问 xmlhttp.responseText 就是接口返回的值
        var content = JSON.parse(xmlhttp.responseText);
        if (content.code === 0) {
            var responseData = content.data;
            // TODO: 请开始你的表演
        } else {
            console.log("请求失败，" + content.msg);
        }
    }
};
// 设置请求的地址并发送请求，其中true表示发送异步请求
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.open("GET", "https://baseurl/common/push/homepage?listSize=10", true);
xmlhttp.send();
```

> **注意：** 上面这段代码我直接抄的菜鸟教程里的，根据实际情况和我的记忆（~~我初学的时候也是直接抄的可以运行~~）稍微改了改，但是没有实际测试验证过。所以仅保证思路和语法正确，不能保证直接复制代码也能运行。

## 3.  jQuery

与原生JavaScript相比，jQuery的语法就要简单得多，而且还不需要担心跨浏览器的兼容性问题。这一点曾经让我对jQuery爱不释手，任何项目甭管大小统统先引入一个jQuery进来。

jQuery发送Ajax请求需要使用`jQuery.ajax()`方法。多说无益，看下面这段代码就能轻易与原生写法一较高下了：

```javascript
var ajaxObj = $.ajax({
    url: "https://baseurl/common/push/homepage",
    data: {
        "listSize": 10
    },
    dataType: "json",
    type: "GET",
    success: function(content) {
        if (content.code === 0) {
            var responseData = content.data;
            // TODO: 请开始你的表演
        } else {
            console.log("请求失败，" + content.msg);
        }
    },
    error: function(xmlhttp, err) {
        console.log("请求错误");
    }
});
```

如果不需要处理error事件，还可以使用更简单的`jQuery.get()`方法。有关jQuery发送Ajax请求更详尽的介绍可以参考`W3school`的[jQuery参考手册 - Ajax](https://www.w3school.com.cn/jquery/jquery_ref_ajax.asp)。

和上一节一样，这段代码也不保证绝对能够正常运行，毕竟我没测试过。但是语法是绝对没问题的。

## 4.  axios

Axios是Node.js中的一个第三方库，也是Vue2.x中官方推荐使用的Ajax库。如果你正在使用Node.js开发前端项目的话，我还是非常推荐大家试试axios的，总之这是一个非常优秀也非常完备的Ajax库。

Axios使用如下的代码来发送GET请求：

```javascript
let data = {
    listSize: 20
};
/*
  注意：
  这里之所以使用let而不是用var，是因为我从接触Node.js开始就改成用WebStorm编写前端项目了。
  在WebStorm里面如果使用了var关键字会报警告，但是如果非要用也没什么大问题。
*/
axios.get("https://baseurl/common/push/homepage", {params: data}).then(result => {
    let content = result.data;
    if (content.code === 0) {
        let responseData = content.data;
        // TODO: 请开始你的表演
    } else {
        console.log("请求失败，" + content.msg);
    }
}).catch(err => {
    console.log("请求错误");
});
```

# POST请求

## 1.  接口文档

假设有如下接口：

- **功能：** 上传并发布新的博客
- **地址：** `https://baseurl/blogManagement/baseInformation/upload`
- **请求方式：** POST
- **请求头：** 

| 参数名       | 是否必须 | 类型   | 说明                       |
| ------------ | -------- | ------ | -------------------------- |
| Content-Type | 是       | string | 请求类型：application/json |

- **请求参数：** 

| 参数名     | 是否必须 | 类型    | 说明                                    |
| ---------- | -------- | ------- | --------------------------------------- |
| title      | 是       | String  | 博客的标题                              |
| isOriginal | 是       | Boolean | 该博客是否为原创，false-转载，true-原创 |
| blurb      | 否       | String  | 博客简介，默认为正文的第一自然段        |
| content    | 是       | String  | 博客正文                                |

- **返回示例：**

正确时返回：

```json
{
    "code": 0,
    "msg": "请求成功",
    "data": {
        "id": 10
    }
}
```

错误时返回：

```json
{
    "code": 4030,
    "msg": "文章正文包含违规内容"
}
```

- **返回参数说明：** 

| 参数名  | 类型   | 说明                           |
| ------- | ------ | ------------------------------ |
| code    | Number | 状态代码，请求成功则为0        |
| msg     | String | 请求状态，作为对请求代码的说明 |
| data    | Object | 返回值，仅当请求成功时存在     |
| data.id | Number | 已上传的文章的ID               |

## 2. 原生JavaScript

我们在小学二年级就学过，POST请求的参数是不会添加到url中的。所以在POST请求中，Ajax的参数应该放到`send`方法中。代码如下：

```javascript
// 首先根据浏览器的不同创建xmlhttp对象
var xmlhttp;
if (window.XMLHttpRequest) {
    xmlhttp = new XMLHttpRequest(); // IE7+, Firefox, Chrome, Opera, Safari
} else {
    xmlhttp = new ActiveXObject("Microsoft.XMLHTTP"); // IE6, IE5
}
// 设置请求的地址并发送请求，其中true表示发送异步请求
xmlhttp.open("POST", "https://baseurl/blogManagement/baseInformation/upload", true);
xmlhttp.setRequestHeader("Content-type","application/json;charset-UTF-8");
xmlhttp.send(JSON.stringify({
    title: 'Ajax的三种写法',
    isOriginal: true,
    blurb: '本篇文章总结并对比了使用原生JavaScript、jQuery和Node.js中的axios库通过Ajax技术请求后端接口时的不同写法。',
    content: '为了防止递归，此处省略2000字'
}));
// 为xmlhttp对象绑定响应函数，该函数每当请求状态改变时就会被触发一次
xmlhttp.onreadystatechange = function() {
    if (xmlhttp.readyState === 4 && xmlhttp.status === 200) {
        // 此时访问 xmlhttp.responseText 就是接口返回的值
        var content = JSON.parse(xmlhttp.responseText);
        if (content.code === 0) {
            var responseData = content.data;
            // TODO: 请开始你的表演
        } else {
            console.log("请求失败，" + content.msg);
        }
    }
};
```

代码非常的多，结果非常的amazing。

## 3. jQuery

作为一个优秀的前端JavaScript库（关于jQuery究竟是库还是框架很多人都有自己的看法，官方认为jQuery是库，我的观点和官方一致），jQuery显然已经对Ajax做了很好的封装，只需要把方法名从GET替换为POST即可。

jQuery使用如下的代码来发送POST请求：

```javascript
var ajaxObj = $.ajax({
    url: "https://baseurl/blogManagement/baseInformation/upload",
    data: {
        title: 'Ajax的三种写法',
        isOriginal: true,
        blurb: '本篇文章总结并对比了使用原生JavaScript、jQuery和Node.js中的axios库通过Ajax技术请求后端接口时的不同写法。',
        content: '为了防止递归，此处省略2000字'
    },
    dataType: "json",
    type: "POST",
    success: function(content) {
        if (content.code === 0) {
            var responseData = content.data;
            // TODO: 请开始你的表演
        } else {
            console.log("请求失败，" + content.msg);
        }
    },
    error: function(xmlhttp, err) {
        console.log("请求错误");
    }
});
```

jQuery赛高！

## 4. axios

Axios中使用POST方法和GET方法相似，除了将方法名从`axios.get`改为`axios.post`之外，还需要修改一下传入参数的方式，具体的写法如下：

```javascript
let data = {
    title: 'Ajax的三种写法',
    isOriginal: true,
    blurb: '本篇文章总结并对比了使用原生JavaScript、jQuery和Node.js中的axios库通过Ajax技术请求后端接口时的不同写法。',
    content: '为了防止递归，此处省略2000字'
}
// 接下来请注意data参数传入的方式与get方法的不同之处
axios.post('https://baseurl/blogManagement/baseInformation/upload', data).then(result => {
    let content = result.data;
    if (content.code === 0) {
        let responseData = content.data;
        // TODO: 请开始你的表演
    } else {
        console.log("请求失败，" + content.msg);
    }
}).catch(err => {
    console.log("请求错误");
});
```

猴！写到这里，相信有很多小伙伴都在感叹，原来在JavaScript中发送Ajax请求是一件这么简单的事情。但事实就是这样，小编对此也感到十分惊讶呢。以上就是在JavaScript中使用三种不同的方法分别发送Ajax请求时的不同写法的总结了，大家还有什么想法呢？欢迎在评论区告诉小编一起讨论哦！
