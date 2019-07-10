# AJAX
## 1.概述
>### ``` Web程序最初的目的就是将信息（数据）放到公共的夫服务器,让所有的网络用户都可以通过浏览器访问。```
>**AJAX就是浏览器提供的一套API，可以通过JavaScript调用，从而实现通过代码控制请求与响应。实现网络编程。**
>
>### ```能力不够API凑。```
## 2.快速上手
使用AJAX的过程可以类比我们访问网页的过程

```
// 1. 创建一个 XMLHttpRequest 类型的对象 —— 相当于打开了一个浏览器

  var xhr = new XMLHttpRequest()
  
// 2. 打开与一个网址之间的连接 —— 相当于在地址栏输入访问地址 

  xhr.open('GET', './time.php')
// 3. 通过连接发送一次请求 —— 相当于回车或者点击访问发送请求 

  xhr.send(null)
  
// 4. 指定 xhr 状态变化事件处理函数 —— 相当于处理网页呈现后的操作
  
  xhr.onreadystatechange = function () {
// 通过 xhr 的 readyState 判断此次请求的响应是否接收完成 

  if(this.readyState === 4){ 
  
// 通过 xhr 的 responseText 获取到响应的响应体     
  console.log(this) 
      } 
  }
```
### 2.1readyState

由于```readystatechange```事件是在```xhr```对象状态变化时触发（不单是在得到响应时），也就意味着这个事件会被出发多次，所以我们有必要了解每一个状态值代表的含义 :
readyState | 状态描述 | 说明
---|---|---
0 | UNSENT| 代理（XHR）被创建，但尚未调用```open()```方法
1 | OPENED|```open()```方法已经被调用，建立了链接。
2|HEADERS_RECEIVED|```send()```方法已经被调用，并且已经可以获取状态行和响应头。
3|LOADING|响应体下载中，```responseText```属性可能已经包含部分数据。
4|DONE|响应体下载完成，可以直接使用```responseText```。

```
var xhr = new XMLHttpRequest()
    console.log(xhr.readyState)
    // => 0
    // 初始化 请求代理对象

    xhr.open('GET', 'time.php')
    console.log(xhr.readyState)
    // => 1
    // open 方法已经调用，建立一个与服务端特定端口的连接

    xhr.send()

    xhr.addEventListener('readystatechange', function () {
      switch (this.readyState) {
        case 2:
          // => 2
          // 已经接受到了响应报文的响应头

          // 可以拿到头
          // console.log(this.getAllResponseHeaders())
          console.log(this.getResponseHeader('server'))
          // 但是还没有拿到体
          console.log(this.responseText)
          break

        case 3:
          // => 3
          // 正在下载响应报文的响应体，有可能响应体为空，也有可能不完整
          // 在这里处理响应体不保险（不可靠）
          console.log(this.responseText)
          break

        case 4:
          // => 4
          // 一切 OK （整个响应报文已经完整下载下来了）
          console.log(this.responseText)
          break
      }
    })
```
通过理解每一个状态值的含义得出一个结论 :一般我们都是在```readyState```值为```4```时，执行响应后的逻辑。
```
xhr.onreadystatechange = function () {
    if (this.readyState === 4){
        //后续逻辑......
    }
}
```
## 2.2遵循HTTP
本质上 XMLHttpRequest 就是 JavaScript 在 Web 平台中发送 HTTP 请求的手段，所以我们发送出去的请求任然是 HTTP 请求，同样符合 HTTP 约定的格式：
```
/ 设置请求报文的请求行
xhr.open('GET',  './time.php')
// 设置请求头
xhr.setRequestHeader('Accept',  'text/plain')
// 设置请求体
xhr.send(null)

xhr.onreadystatechange = function () {
if (this.readyState === 4) {
// 获取响应状态码
console.log(this.status)
// 获取响应状态描述
console.log(this.statusText)
// 获取响应头信息
console.log(this.getResponseHeader('Content‐Type'))  
//  指定响应头 
console.log(this.getAllResponseHeader())  
//  全部响应头
// 获取响应体
console.log(this.responseText)  //  文本形式
console.log(this.responseXML)  //  XML  形式，了解即可不用了
 }
}
```

>###  ``` 参考链接```
>###### - https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest
>###### - https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest


## 3.具体用法

### 3.1GET请求
>#### ```通常在一次 GET 请求过程中，参数传递都是通过 URL 地址中的 ```?``` 参数传递。```
``` 
var xhr = new XMLHttpRequest()
// GET 请求传递参数通常使用的是问号传参
// 这里可以在请求地址后面加上参数，从而传递数据到服务端
xhr.open('GET',  './delete.php?id=1')
// 一般在 GET 请求时无需设置响应体，可以传 null 或者干脆不传
xhr.send(null)
xhr.onreadystatechange = function () { if (this.readyState === 4) {
console.log(this.responseText)
}
}

```
### 3.2POST请求

>#### ```POST 请求过程中，都是采用请求体承载需要提交的数据。```

```
1 > var xhr = new XMLHttpRequest()
2 > //  open  方法的第一个参数的作用就是设置请求的  method
3 > xhr.open('POST',  './add.php')
4 > //  设置请求头中的  Content‐Type  为  application/x‐www‐form‐urlencoded
5 > // 标识此次请求的请求体格式为 urlencoded 以便于服务端接收数据
6 > xhr.setRequestHeader('Content‐Type',  'application/x‐www‐form‐urlencoded')
7 > // 需要提交到服务端的数据可以通过 send 方法的参数传递
8 > //  格式：key1=value1&key2=value2
9 > xhr.send('key1=value1&key2=value2')
10 >xhr.onreadystatechange = function () {
11 > if (this.readyState === 4) {
12 > console.log(this.responseText) 13	}
14 >	}
```
### 3.3同步与异步
关于同步与异步的概念在生活中有很多常见的场景，举例说明。
>#### ```同步：一个人在同一个时刻只能做一件事情，在执行一些耗时的操作（不需要看管）不去做别的事，只是等待```
>#### ```异步：在执行一些耗时的操作（不需要看管）去做别的事，而不是等待```
### 3.4.响应式数据格式
>#### ```提问：如果希望服务端返回一个复杂数据，该如何处理？```
关心的问题就是服务端发出何种格式的数据，这种格式如何在客户端用 JavaScript 解析。
### 3.4.1XML
一种数据描述手段（已淘汰）

淘汰的原因：数据冗余太多

### 3.42.JSON
也是一种数据描述手段，类似于 JavaScript

字面量方式

服务端采用 JSON 格式返回数据，客户端按照 JSON 格式解析数据。
>#### ```不管是 JSON 也好，还是 XML，只是在 A JAX 请求过程中用到，并不代表它们之间有必然的联系，它们只是数据协议罢了```
### 3.5处理响应式数据渲染
>#### ``` 模板引擎 : ```
>#### ``` artTemplate：https://aui.github.io/art-template/``` 
### 3.6.兼容方案
XMLHttpRequest 在老版本浏览器（IE5/6）中有兼容问题，可以通过另外一种方式代替

```var  xhr  =  window.XMLHttpRequest  ?  new  XMLHttpRequest()  :  new ActiveXObject('Microsoft.XMLHTTP')```
## 4.封装
### 4.1AJAX请求封装

>
```函数就可以理解为一个想要做的事情，函数体中约定了这件事情做的过程，直到调用时才开始工作。将函数作为参数传递就像是将一个事情交给别人，这就是委托的概念```

```
/**
2* 发送一个 AJAX 请求
3*  @param    {String}	method  请求方法
4*  @param    {String}	url	请求地址
5*  @param    {Object}	params  请求参数
6*  @param    {Function}  done	请求完成过后需要做的事情（委托/回调）
7	*/
8function  ajax  (method,  url,  params,  done)  {
9// 统一转换为大写便于后续判断
10method  =  method.toUpperCase() 11
12// 对象形式的参数转换为 urlencoded 格式
13var pairs = []
14for  (var  key  in  params)  {
15pairs.push(key  +  '='  +  params[key]) 16	}
17	var  querystring  =  pairs.join('&') 18
19		var  xhr  =  window.XMLHttpRequest  ?  new  XMLHttpRequest()  :  new ActiveXObject('Microsoft.XMLHTTP')
20
21xhr.addEventListener('readystatechange',  function  ()  {
22if (this.readyState !== 4) return 23
24// 尝试通过 JSON 格式解析响应体
25try {
26done(JSON.parse(this.responseText))
27} catch (e) {
28done(this.responseText)
29	}
30	})
31
32// 如果是 GET 请求就设置 URL 地址 问号参数
33if  (method  ===  'GET')  {
34url  +=  '?'  +  querystring
35	}
36
37	xhr.open(method,  url) 38
39// 如果是 POST 请求就设置请求体
40var data = null
41if  (method  ===  'POST')  {
42xhr.setRequestHeader('Content‐Type',  'application/x‐www‐form‐urlencoded')
43data = querystring
44	}
45	xhr.send(data)
46	}

47
```
### 4.2jQuery 中的AJAX
>#### ```参考 ```
>#### ```http://www.jquery123.com/category/ajax/ ```
### 4.2.1 $.ajax
```// // 最基础的 调用
    // $.ajax('./time.php', {
    //   type: 'post', // method 请求方法
    //   success: function (res) {
    //     // res => 拿到的只是响应体
    //     console.log(res)
    //   }
    // })
        
    // $.ajax({
    //   url: 'time.php',
    //   type: 'post',
    //   // 用于提交到服务端的参数，
    //   // 如果是 GET 请求就通过 url 传递
    //   // 如果是 POST 请求就通过请求体传递
    //   data: { id: 1, name: '张三' },
    //   success: function (res) {
    //     console.log(res)
    //   }
    // })

    // $.ajax({
    //   url: 'json.php',
    //   type: 'get',
    //   success: function (res) {
    //     // res 会自动根据服务端响应的 content-type 自动转换为对象
    //     // 这是 jquery 内部实现的
    //     console.log(res)
    //   }
    // })
    
    $.ajax({
      url: 'json.php',
      type: 'get',
      // 设置的是请求参数
      data: { id: 1, name: '张三' },
      // 用于设置响应体的类型 注意 跟 data 参数没关系！！！
      dataType: 'json',
      success: function (res) {
        // 一旦设置的 dataType 选项，就不再关心 服务端 响应的 Content-Type 了
        // 客户端会主观认为服务端返回的就是 JSON 格式的字符串
        console.log(res)
      }
    })
```
常用选项参数介绍：
- url：请求地址
- type：请求方法，默认为
- dataType：服务端响应数据类型
- 
- contentType：请求体内容类型，默认
- data：需要传递到服务端的数据，如果 GET 则通过 URL 传递，如果 POST 则通过请求体传递
- timeout：请求超时时间
- beforeSend：请求发起之前触发
- success：请求成功之后触发（响应状态码 200）
- error： 请 求 失 败 触 发           complete：请求完成触发（不管成功与否）


### 4.2.2$.get和$.post

GET请求快捷方法和POST快捷请求方法

```
$.get('json.php', { id: 1 }, function (res) {
      console.log(res)
    })

    $.post('json.php', { id: 1 }, function (res) {
      console.log(res)
    })

    $.getJSON('json.php', { id: 1 }, function (res) {
      console.log(res)
    })
```
### 4.2.3 jQuery中AJAX的回调

原生操作中不管请求状态码是多少都会触发回调

```
    // var xhr = new XMLHttpRequest()
    // xhr.open('get', 'time1.php')
    // xhr.send()
    // xhr.onreadystatechange = function () {
    //   if (this.readyState !== 4) return
    //   console.log(this.responseText)
    // }
```
jQuery中的AJAX回调
```
$.ajax({
      url: 'time.php',
      type: 'get',
      beforeSend: function (xhr) {
        // 在所有发送请求的操作（open, send）之前执行
        console.log('beforeSend', xhr)
      },
      success: function (res) {
        // 隐藏 loading
        // 只有请求成功（状态码为200）才会执行这个函数
        console.log(res)
      },
      error: function (xhr) {
        // 隐藏 loading
        // 只有请求不正常（状态码不为200）才会执行
        console.log('error', xhr)
      },
      complete: function (xhr) {
        // 不管是成功还是失败都是完成，都会执行这个 complete 函数
        console.log('complete', xhr)
      }
    })
```

### 4.2.4 jQuery全局事件处理函数
html
```
<div class="loading">正在玩命加载中...</div>
  <button id="btn">请求</button>
```
css
```
.loading {
      display: none;
      position: fixed;
    }
```
jQuety
```
$(document)
      .ajaxStart(function () {
        // 只要有 ajax 请求发生 就会执行
        $('.loading').fadeIn()
        // 显示加载提示
        console.log('注意即将要开始请求了')
      })
      .ajaxStop(function () {
        // 只要有 ajax 请求结束 就会执行
        $('.loading').fadeOut()
        // 结束提示
        console.log('请求结束了')
      })

    $('#btn').on('click', function () {
      // $.ajax({
      //   url: 'time.php'
      // })

      $.get('time.php')
    })
```
## 5.跨域

### 5.1相关概念
**同源策略**是浏览器的一中安全策略，所谓同源是指域名，协议，端口完全相同，只有同源的地址才可以相互通过AJAX的方式请求。

同源或者不同源是指两个地址之间的关系，不同源地址之间的请求称之为**跨域请求**
### 5.2解决方案
**JSON** with **Padding**，是一种借助于 script 标签发送跨域请求的技巧。

其原理就是在客户端借助 ```script``` 标签请求服务端的一个动态网页（php 文件），服务端的这个动态网页返回一
段带有函数调用的 JavaScript 全局函数调用的脚本，将原本需要返回给客户端的数据传递进去。

以后绝大多数情况都是采用 JSONP 的手段完成不同源地址之间的跨域请求

客户端
```
<script src="http://api.zce.me/users.php?callback=foo"></script>
```
服务端返回结果
```
foo(['我', '是', '你', '原', '本', '需', '要', '的', '数', '据'])
```
**总结** ：由于 XMLHttpRequest 无法发送不同源地址之间的跨域请求，所以我们必须要另寻他法，(~~img~~,~~link~~可以发送不同源地址之间的请求无法拿到响应结果)**script**这种方
案就是我们最终选择的方式，我们把这种方式称之为 JSONP。

问题：

   1. JSONP 需要服务端配合，服务端按照客户端的要求返回一段 JavaScript 调用客户端的函数
   2. 只能发送 GET 请求

   jQuery 中使用 JSONP 就是将 dataType 设置为 jsonp


```
 $.ajax({
      url: 'http://localhost/jsonp/server.php',
      dataType: 'jsonp',
      success: function (res) {
        console.log(res)
      }
    })
```
其他常见的AJAX封装库：
- Axios

### 5.2.2CORS
Cross Origin Resource Share，跨域资源共享
```
// 允许远端访问
header('Access‐Control‐Allow‐Origin: *');
```
这种方案无需客户端作出任何变化（客户端不用改代码），只是在被请求的服务端响应的时候添加一个 AccessControl-Allow-Origin 的响应头，表示这个资源是否允许指定域请求。










​    

