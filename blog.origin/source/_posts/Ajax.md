---
title: Ajax
date: 2020-03-20 10:38:40
tags: [Ajax]
---


### AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

### AJAX 用于和服务器交换数据，是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

### XMLHttpRequest 对象 是 AJAX 的基础

### 使用
```js
var xmlhttp = new XMLHttpRequest(); // ie 6以上
var xmlhttp =new ActiveXObject("Microsoft.XMLHTTP"); // ie5 ie6

/**
 * @description: 规定请求的类型、URL 以及是否异步处理请求。
 * @param {Sting} method：请求的类型；GET 或 POST
 * @param {Sting} url：文件在服务器上的位置
 * @param {Boolean} async：true（异步）或 false（同步）
 * @return: 
 */
xmlhttp.open(method,url,async);
/**
 * @description: 将请求发送到服务器。。
 * @param {Sting} 仅用于 POST 请求
 * /
xmlhttp.send(string)
```

### 例子
```js
// get方法
xmlhttp.open("GET","demo_get2.asp?fname=Bill&lname=Gates",true);
xmlhttp.send();
// post方法
xmlhttp.open("POST","ajax_test.asp",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```

### 设置
```js
/**
 * @description: 向请求添加 HTTP 头。
 * @param {type} header: 规定头的名称
 * @param {type} value: 规定头的值
 * @return: 
 */
setRequestHeader(header,value)
```

### 响应处理
onreadystatechange 事件，存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。
获得来自服务器的响应，请使用 XMLHttpRequest 对象的 responseText 或 responseXML 属性。

属性 | 	描述
-|-
responseText  | 获得字符串形式的响应数据。
responseXML | 获得 XML 形式的响应数据。
readyState | 存有 XMLHttpRequest 的状态。 <br/>从 0 到 4 发生变化。<br/>0: 请求未初始化<br/>1: 服务器连接已建立<br/>2: 请求已接收<br/>3: 请求处理中<br/>4: 请求已完成，且响应已就绪
status| 200: "OK" 404: 未找到页面      



```js
//async=true 时 请规定在响应处于 onreadystatechange 事件中的就绪状态时执行的函数
Async = true
xmlhttp.onreadystatechange=function()
  {
  if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
  }
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();

// Async = false 不要编写 onreadystatechange 函数 - 把代码放到 send() 语句后面即可
xmlhttp.open("GET","test1.txt",false);
xmlhttp.send();
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

#### 示例1
```js
const ajax = function（options = {}） {
    options.type = ( options.type || 'GET' ).toUpperCase();
    
    let data = [];
    for (let i  in options.data) {
      data.push(encodeURIComponent(i) + "=" + encodeURIComponent(otions.data[i]));
    }

    data = data.join('&');

    const xhr = new XMLHttpRequest();
    xhr.onreadstatechange = function（） {
        if（xhr.readyState === 4）{
            const status = xhr.status;
            if (status >= 200 && status <300) {
                options.success && options.success(JSON.parse(xhr.responseText), xhr.responseXML);
            } else {
                option.error && options.error(status);
            }
        }
    }
    if (options.type === 'GET') {
        xhr.open('GET', options.url + '?' + data, true);
        xhr.setRequestHeader('Content-Type', 'application/x-www-foem-urlencoded');
        xhr.send(data);
    }
};
```

 #### 示例2
```js
// Ajax 封装
var isrequesting = false;
function ajax(action, data, successcallback, errorcallback) {
    if (isrequesting) {
        return false;
    }

    isrequesting = true;
    try {
        $.ajax({
            url: action,
            type: "post",
            dataType: "json",
            data: data,
            error: function (ex, b, c) {
                isrequesting = false;
                if (errorcallback) {
                    errorcallback(ex, b, c);
                }
            },
            success: function (data) {
                isrequesting = false;
                successcallback(data);
            }
        });
    } catch (ex) {
        isrequesting = false;
    }
}
```