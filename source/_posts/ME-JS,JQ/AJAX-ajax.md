---
title: ajax
date: 2016-07-19
tags: ajax
categories: JS/JQ
---
------

AJAX 是与服务器交换数据并更新部分网页的艺术，是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

<!-- more -->

## ajax 原理



### 创建 XMLHttpRequest 对象

```js
var obj = new XMLHttpRequest();
```
### 向服务器发送请求

> 如需将请求发送到服务器，我们使用 XMLHttpRequest 对象的 open() 和 send() 方法：

```js
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();
```
| 方法        | 描述    |
| :----:      | :----:  |
| open(method,url,async)        | 规定请求的类型、URL 以及是否异步处理请求。</br> method：请求的类型；GET 或 POST </br>url：文件在服务器上的位置 </br>async：true（异步）或 false（同步）     |
| send(string)        |     将请求发送到服务器。</br> string：仅用于 POST 请求      |

###  open需要三个参数

* 第一个参数定义发送请求所使用的方法（GET 还是 POST）

> GET 还是 POST？
> 与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。
> 然而，在以下情况中，请使用 POST 请求：
> * 无法使用缓存文件（更新服务器上的文件或数据库）
> * 向服务器发送大量数据（POST 没有数据量限制）
> * 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠


* 第二个参数规定服务器端脚本的 URL(该文件可以是任何类型的文件，比如 .txt 和 .xml，或者服务器脚本文件，比如 .asp 和 .php （在传回响应之前，能够在服务器上执行任务）)。

* 第三个参数规定应当对请求进行异步地处理(true（异步）或 false（同步）)。

> 异步 - True 或 False？

> 对于 web 开发人员来说，发送异步请求是一个巨大的进步。很多在服务器执行的任务都相当费时。AJAX 出现之前，这可能会引起应用程序挂起或停止。

> 我们不推荐使用 async=false，但是对于一些小型的请求，也是可以的。
请记住，JavaScript 会等到服务器响应就绪才继续执行。如果服务器繁忙或缓慢，应用程序会挂起或停止。

> send() 方法可将请求送往服务器。

> onreadystatechange 属性存有处理服务器响应的函数。

> readyState 属性存有服务器响应的状态信息。每当 readyState 改变时，onreadystatechange 函数就会被执行。

> readyState状态值
>> * 0: 请求未初始化
>> * 1: 服务器连接已建立
>> * 2: 请求已接收
>> * 3: 请求处理中
>> * 4: 请求已完成，且响应已就绪

> status状态

> 200: "OK"
> 404: 未找到页面

## 原生ajax写法

```js
var Ajax={
    get: function (url,fn){
        var obj=new XMLHttpRequest();  // XMLHttpRequest对象用于在后台与服务器交换数据          
        obj.open('GET',url,true);
        obj.onreadystatechange=function(){
            if (obj.readyState == 4 && obj.status == 200 || obj.status == 304) { // readyState==4说明请求已完成
                fn.call(this, obj.responseText);  //从服务器获得数据
            }
        };
        obj.send(null);
    },
    post: function (url, data, fn) {
        var obj = new XMLHttpRequest();
        obj.open("POST", url, true);
        obj.setRequestHeader("Content-type", "application/x-www-form-urlencoded"); // 发送信息至服务器时内容编码类型
        obj.onreadystatechange = function () {
            if (obj.readyState == 4 && (obj.status == 200 || obj.status == 304)) {  // 304未修改
                fn.call(this, obj.responseText);
            }
        };
        obj.send(data);
    }
}
```

http://www.w3school.com.cn/ajax/index.asp

### Ajax中同步和异步的概念
 var xhr=new XMLHttpRequest()
 xhr.open('get','/robot.txt',true)
 xhr.send()
 document.body.innerHTML +=xhr.response;



 var xhr=new XMLHttpRequest();  
 xhr.addEventListener('readystatechange',function(){  //检测到状态改变
            // console.log(xhr.response)
            if( this.readyState !== 4){  状态改变
                return;
            }
            if( this.status === 200 || this.status === 304){
                document.body.innerHTML += xhr.response
            }
         })
        xhr.open('get','/robot.txt',true);
        xhr.send();
 x 随意  ML  标记语言  HTTP 超文本传输协议  Request 请求

执行队列

异步

可执行 待执行

## ajax问题

### 怎么使用ajax上传图片

* 建议图片一定通过异步上传。就是单独开一个接口。避免上传图片
```js
// 获取其他表单数据
var form=document.forms[0];
var formData = new FormData(form);   //这里连带form里的其他参数也一起提交了,如果不需要提交其他参数可以直接FormData无参数的构造函数
//convertBase64UrlToBlob函数是将base64编码转换为Blob
formData.append("imageName",convertBase64UrlToBlob(base64Codes));  //append函数的第一个参数是后台获取数据的参数名,和html标签的input的name属性功能相同

/**
 * 将以base64的图片url数据转换为Blob
 * @param urlData
 *            用url方式表示的base64图片数据
 */
function convertBase64UrlToBlob(urlData){

    var bytes=window.atob(urlData.split(',')[1]);        //去掉url的头，并转换为byte

    //处理异常,将ascii码小于0的转换为大于0
    var ab = new ArrayBuffer(bytes.length);
    var ia = new Uint8Array(ab);
    for (var i = 0; i < bytes.length; i++) {
        ia[i] = bytes.charCodeAt(i);
    }

    return new Blob( [ab] , {type : 'image/png'});
}

//创建FormData对象
var data = new FormData();
//为FormData对象添加数据
var file = $('#inputfile')[0].files[0];
data.append('upload_file', file); //第一个参数为参数名，第二个为参数值
console.log(data.get('imageName'));
$.ajax({
    url: "http://www.baidu.com", //请求的url地址
    type: "POST", //请求方式 GET
    data: data, //参数值
    cache: false,
    contentType: false,     // 告诉jQuery不要去处理发送的数据
    processData: false,     // 告诉jQuery不要去设置Content-Type请求头
    beforeSend: function () {//请求前的处理
        alert("正在加载");
    },
    success: function (req) { //请求成功时处理
        console.log(req);
    },
    complete: function () { //请求完成的处理
        alert("修改完成");
    },
    error: function (req) { //请求出错处理
        console.log(req);
    }
});
```


### ajax请求返回状态为200但还是进入error事件

* 最近遇到一个问题，发送一个ajax请求，请求成功了，并且放回状态为200，但是就是不进入success事件，添加error事件竟进入了error事件。
```js
$.ajax({  
    url:$WEB_ROOT_PATH+"/dataLevel/dataLevelCtrl.htm?BLHMI=findBasicDataLevel",  
    type:"post",  
    dataType:"json",  
    async:false,  
    success:function(data){  
        var dataScore = data;  
    },error:function(){  
        alert("出错啦！");  
    }  
});  
```
* 出错原因：dataType:"json",而后台返回的数据不符合json规范。
解决方法：先将dataType设置为text，这样就可以进入success方法了，查看data数据究竟是什么。
我的data为：｛"success":success｝，可以看出第二个success没有引号包裹，不符合json规范，故而不能转换为json对象。
之后的解决方法就很好办了。一种是修改后台返回值，二种是直接解析text返回的值。

### [PHP Ajax 跨域问题最佳解决方案](http://www.runoob.com/w3cnote/php-ajax-cross-border.html)

* 本文通过设置**Access-Control-Allow-Origin**来实现跨域。
例如：客户端的域名是client.runoob.com，而请求的域名是server.runoob.com。
如果直接使用ajax访问，会有以下错误：
XMLHttpRequest cannot load http://server.runoob.com/server.php. No 'Access-Control-Allow-Origin' header is present on the requested resource.Origin 'http://client.runoob.com' is therefore not allowed access.
#### 1、允许单个域名访问
指定某域名（http://client.runoob.com）跨域访问，则只需在http://server.runoob.com/server.php文件头部添加如下代码：
header('Access-Control-Allow-Origin:http://client.runoob.com');
#### 2、允许多个域名访问
指定多个域名（http://client1.runoob.com、http://client2.runoob.com等）跨域访问，则只需在http://server.runoob.com/server.php文件头部添加如下代码：
$origin = isset($_SERVER['HTTP_ORIGIN'])? $_SERVER['HTTP_ORIGIN'] : ''; $allow_origin = array(  
    'http://client1.runoob.com',  
    'http://client2.runoob.com'  
);  
if(in_array($origin, $allow_origin)){
     header('Access-Control-Allow-Origin:'.$origin);      
}
#### 3、允许所有域名访问
允许所有域名访问则只需在http://server.runoob.com/server.php文件头部添加如下代码：
header('Access-Control-Allow-Origin:*');



## ajax4种方式

### $.ajax(),$.getjson(),$.get(),$.post()的区别和差异


### $.ajax()

* $.ajax()是jquery中通用的一个ajax封装，其语法的格式为：
$.ajax(options)
其中options是一个object类型，它指明了本次ajax调用的具体参数，这里我把最常用的几个参数附上
```js
// 新写法
// 代码更加美化

$.ajax({
  url: '/path/to/file',
  type: 'default GET (Other values: POST)',
  dataType: 'default: Intelligent Guess (Other values: xml, json, script, or html)',
  data: {param1: 'value1'}
})
.done(function() {
  console.log("success");
})
.fail(function() {
  console.log("error");
})
.always(function() {
  console.log("complete");
});
```
```js
$.ajax({
    url: "",  
    type: "POST", //请求方式 GET
    async: false,   
    data: { "xx": "xxx" },
    dataType: "json",
    beforeSend: function () {
        console.log("正在加载");
    },
    success: function (req) {  
        console.log(req);
    },
    complete: function () {
        console.log("修改完成");
    },
    error: function (req) {  
        console.log(req);
    }
});
$.ajax({
    url: "", //请求的url地址
    type: "POST", //请求方式 GET
    async: false,  //请求是否异步，默认为异步，这也是ajax重要特性 飞鸟项目默认为同步了 注意，同步请求将锁住浏览器，用户其它操作必须等待请求完成才可以执行。
    data: { "xx": "xxx" }, //参数值
    dataType: "json", //返回格式为json
    beforeSend: function () {//请求前的处理
        console.log("正在加载");
    },
    success: function (req) { //请求成功时处理
        console.log(req);
    },
    complete: function () { //请求完成的处理
        console.log("修改完成");
    },
    error: function (req) { //请求出错处理
        console.log(req);
    }
});
```
### $.getJSON()

* $.getJSON()是专门为ajax获取json数据而设置的，并且支持跨域调用，其语法的格式为：
getJSON(url,[data],[callback])
url：string类型， 发送请求地址  data ：可选参数， 待发送 Key/value 参数 ，同get，post类型的data callback ：可选参数，载入成功时回调函数，同get，post类型的callback
JSON是一种理想的数据传输格式，它能够很好的融合与JavaScript或其他宿主语言，并且可以被JS直接使用。使用JSON相比传统的通过 GET、POST直接发送“裸体”数据，在结构上更为合理，也更为安全。至于jQuery的getJSON()函数，只是设置了JSON参数的 ajax()函数的一个简化版本。这个函数也是可以跨域使用的，相比get()、post()有一定优势。另外这个函数可以通过把请求url写 成"myurl?callback=X"这种格式，让程序执行回调函数X。

```js
$.getJSON("http://api.flickr.com/services/feeds/photos_public.gne?tags=cat&tagmode=any&format=json&jsoncallback=?",function(req){
    $.each(req.items,function(i, item){
        if(i == vnum){
            $("<img src="" + item.media.m + "" title="" + item.title + "" />").appendTo("#showjson");
        }
    });
});
```

### $.post()
* $.post()方法使用POST方式来进行异步请求，它的语法结构为：
$.post(url,[data],[callback],[type])
这个方法和$.get()用法差不多，唯独多了一个type参数，那么这里就只介绍type参数吧，其他的参考上面$.get()的。
type：type为请求的数据类型，可以是html,xml,json等类型，如果我们设置这个参数为：json，那么返回的格式则是json格式的，如果没有设置，就和$.get()返回的格式一样，都是字符串的。
```js
$.post(
    "submit.aspx",{
        id:     '2016',
        name:   '笔者',
    },function(data,state){
        //这里显示从服务器返回的数据
        console.log(data);
        //这里显示返回的状态
        console.log(state);
    },
    "json"
)
```

### $.get()

* $.get()方法使用GET方式来进行异步请求，它的语法结构为：
$.get( url [, data] [, callback] )
url：string类型，ajax请求的地址。
data：可选参数，object类型，发送至服务器的key/value数据会作为QueryString附加到请求URL中。
callback：可选参数，function类型，当ajax返回成功时自动调用该函数。
```js
$.get(
    "url",{
        id:     '2016',
        name:   '笔者',
    },function(data,state){
        //这里显示从服务器返回的数据
        console.log(data);
        //这里显示返回的状态
        console.log(state);
    }
)
```


## 参数

<dt>options</dt>

类型：Object

可选。AJAX 请求设置。所有选项都是可选的。

<dt>async</dt>

类型：Boolean

默认值: true。默认设置下，所有请求均为异步请求。如果需要发送同步请求，请将此选项设置为 false。

注意，同步请求将锁住浏览器，用户其它操作必须等待请求完成才可以执行。

<dt>beforeSend(XHR)</dt>

类型：Function

发送请求前可修改 XMLHttpRequest 对象的函数，如添加自定义 HTTP 头。

XMLHttpRequest 对象是唯一的参数。

这是一个 Ajax 事件。如果返回 false 可以取消本次 ajax 请求。

<dt>cache</dt>

类型：Boolean

默认值: true，dataType 为 script 和 jsonp 时默认为 false。设置为 false 将不缓存此页面。

jQuery 1.2 新功能。

<dt>complete(XHR, TS)</dt>

类型：Function

请求完成后回调函数 (请求成功或失败之后均调用)。

参数： XMLHttpRequest 对象和一个描述请求类型的字符串。

这是一个 Ajax 事件。

<dt>contentType</dt>

类型：String

默认值: "application/x-www-form-urlencoded"。发送信息至服务器时内容编码类型。

默认值适合大多数情况。如果你明确地传递了一个 content-type 给 $.ajax() 那么它必定会发送给服务器（即使没有数据要发送）。

<dt>context</dt>

类型：Object

这个对象用于设置 Ajax 相关回调函数的上下文。也就是说，让回调函数内 this 指向这个对象（如果不设定这个参数，那么 this 就指向调用本次 AJAX 请求时传递的 options 参数）。比如指定一个 DOM 元素作为 context 参数，这样就设置了 success 回调函数的上下文为这个 DOM 元素。

就像这样：

$.ajax({ url: "test.html", context: document.body, success: function(){
        $(this).addClass("done");
      }});

<dt>data</dt>

类型：String

发送到服务器的数据。将自动转换为请求字符串格式。GET 请求中将附加在 URL 后。查看 processData 选项说明以禁止此自动转换。必须为 Key/Value 格式。如果为数组，jQuery 将自动为不同值对应同一个名称。如 {foo:["bar1", "bar2"]} 转换为 '&foo=bar1&foo=bar2'。

<dt>dataFilter</dt>

类型：Function

给 Ajax 返回的原始数据的进行预处理的函数。提供 data 和 type 两个参数：data 是 Ajax 返回的原始数据，type 是调用 jQuery.ajax 时提供的 dataType 参数。函数返回的值将由 jQuery 进一步处理。

<dt>dataType</dt>

类型：String

预期服务器返回的数据类型。如果不指定，jQuery 将自动根据 HTTP 包 MIME 信息来智能判断，比如 XML MIME 类型就被识别为 XML。在 1.4 中，JSON 就会生成一个 JavaScript 对象，而 script 则会执行这个脚本。随后服务器端返回的数据会根据这个值解析后，传递给回调函数。可用值:

*   "xml": 返回 XML 文档，可用 jQuery 处理。
*   "html": 返回纯文本 HTML 信息；包含的 script 标签会在插入 dom 时执行。
*   "script": 返回纯文本 JavaScript 代码。不会自动缓存结果。除非设置了 "cache" 参数。注意：在远程请求时(不在同一个域下)，所有 POST 请求都将转为 GET 请求。（因为将使用 DOM 的 script标签来加载）
*   "json": 返回 JSON 数据 。
*   "jsonp": JSONP 格式。使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数。
*   "text": 返回纯文本字符串

<dt>error</dt>

类型：Function

默认值: 自动判断 (xml 或 html)。请求失败时调用此函数。

有以下三个参数：XMLHttpRequest 对象、错误信息、（可选）捕获的异常对象。

如果发生了错误，错误信息（第二个参数）除了得到 null 之外，还可能是 "timeout", "error", "notmodified" 和 "parsererror"。

这是一个 Ajax 事件。

<dt>global</dt>

类型：Boolean

是否触发全局 AJAX 事件。默认值: true。设置为 false 将不会触发全局 AJAX 事件，如 ajaxStart 或 ajaxStop 可用于控制不同的 Ajax 事件。

<dt>ifModified</dt>

类型：Boolean

仅在服务器数据改变时获取新数据。默认值: false。使用 HTTP 包 Last-Modified 头信息判断。在 jQuery 1.4 中，它也会检查服务器指定的 'etag' 来确定数据没有被修改过。

<dt>jsonp</dt>

类型：String

在一个 jsonp 请求中重写回调函数的名字。这个值用来替代在 "callback=?" 这种 GET 或 POST 请求中 URL 参数里的 "callback" 部分，比如 {jsonp:'onJsonPLoad'} 会导致将 "onJsonPLoad=?" 传给服务器。

<dt>jsonpCallback</dt>

类型：String

为 jsonp 请求指定一个回调函数名。这个值将用来取代 jQuery 自动生成的随机函数名。这主要用来让 jQuery 生成度独特的函数名，这样管理请求更容易，也能方便地提供回调函数和错误处理。你也可以在想让浏览器缓存 GET 请求的时候，指定这个回调函数名。

<dt>password</dt>

类型：String

用于响应 HTTP 访问认证请求的密码

<dt>processData</dt>

类型：Boolean

默认值: true。默认情况下，通过data选项传递进来的数据，如果是一个对象(技术上讲只要不是字符串)，都会处理转化成一个查询字符串，以配合默认内容类型 "application/x-www-form-urlencoded"。如果要发送 DOM 树信息或其它不希望转换的信息，请设置为 false。

<dt>scriptCharset</dt>

类型：String

只有当请求时 dataType 为 "jsonp" 或 "script"，并且 type 是 "GET" 才会用于强制修改 charset。通常只在本地和远程的内容编码不同时使用。

<dt>success</dt>

类型：Function

请求成功后的回调函数。

参数：由服务器返回，并根据 dataType 参数进行处理后的数据；描述状态的字符串。

这是一个 Ajax 事件。

<dt>traditional</dt>

类型：Boolean

如果你想要用传统的方式来序列化数据，那么就设置为 true。请参考工具分类下面的 jQuery.param 方法。

<dt>timeout</dt>

类型：Number

设置请求超时时间（毫秒）。此设置将覆盖全局设置。

<dt>type</dt>

类型：String

默认值: "GET")。请求方式 ("POST" 或 "GET")， 默认为 "GET"。注意：其它 HTTP 请求方法，如 PUT 和 DELETE 也可以使用，但仅部分浏览器支持。

<dt>url</dt>

类型：String

默认值: 当前页地址。发送请求的地址。

<dt>username</dt>

类型：String

用于响应 HTTP 访问认证请求的用户名。

<dt>xhr</dt>

类型：Function

需要返回一个 XMLHttpRequest 对象。默认在 IE 下是 ActiveXObject 而其他情况下是 XMLHttpRequest 。用于重写或者提供一个增强的 XMLHttpRequest 对象。这个参数在 jQuery 1.3 以前不可用。

## 回调函数

如果要处理 $.ajax() 得到的数据，则需要使用回调函数：beforeSend、error、dataFilter、success、complete。

### beforeSend

在发送请求之前调用，并且传入一个 XMLHttpRequest 作为参数。

### error

在请求出错时调用。传入 XMLHttpRequest 对象，描述错误类型的字符串以及一个异常对象（如果有的话）

### dataFilter

在请求成功之后调用。传入返回的数据以及 "dataType" 参数的值。并且必须返回新的数据（可能是处理过的）传递给 success 回调函数。

### success

当请求之后调用。传入返回后的数据，以及包含成功代码的字符串。

### complete

当请求完成之后调用这个函数，无论成功或失败。传入 XMLHttpRequest 对象，以及一个包含成功或错误代码的字符串。

## 数据类型

$.ajax() 函数依赖服务器提供的信息来处理返回的数据。如果服务器报告说返回的数据是 XML，那么返回的结果就可以用普通的 XML 方法或者 jQuery 的选择器来遍历。如果见得到其他类型，比如 HTML，则数据就以文本形式来对待。

通过 dataType 选项还可以指定其他不同数据处理方式。除了单纯的 XML，还可以指定 html、json、jsonp、script 或者 text。

其中，text 和 xml 类型返回的数据不会经过处理。数据仅仅简单的将 XMLHttpRequest 的 responseText 或 responseHTML 属性传递给 success 回调函数。

注意：我们必须确保网页服务器报告的 MIME 类型与我们选择的 dataType 所匹配。比如说，XML的话，服务器端就必须声明 text/xml 或者 application/xml 来获得一致的结果。

如果指定为 html 类型，任何内嵌的 JavaScript 都会在 HTML 作为一个字符串返回之前执行。类似地，指定 script 类型的话，也会先执行服务器端生成 JavaScript，然后再把脚本作为一个文本数据返回。

如果指定为 json 类型，则会把获取到的数据作为一个 JavaScript 对象来解析，并且把构建好的对象作为结果返回。为了实现这个目的，它首先尝试使用 JSON.parse()。如果浏览器不支持，则使用一个函数来构建。

JSON 数据是一种能很方便通过 JavaScript 解析的结构化数据。如果获取的数据文件存放在远程服务器上（域名不同，也就是跨域获取数据），则需要使用 jsonp 类型。使用这种类型的话，会创建一个查询字符串参数 callback=? ，这个参数会加在请求的 URL 后面。服务器端应当在 JSON 数据前加上回调函数名，以便完成一个有效的 JSONP 请求。如果要指定回调函数的参数名来取代默认的 callback，可以通过设置 $.ajax() 的 jsonp 参数。

注意：JSONP 是 JSON 格式的扩展。它要求一些服务器端的代码来检测并处理查询字符串参数。

如果指定了 script 或者 jsonp 类型，那么当从服务器接收到数据时，实际上是用了 <script> 标签而不是 XMLHttpRequest 对象。这种情况下，$.ajax() 不再返回一个 XMLHttpRequest 对象，并且也不会传递事件处理函数，比如 beforeSend。

## 发送数据到服务器

默认情况下，Ajax 请求使用 GET 方法。如果要使用 POST 方法，可以设定 type 参数值。这个选项也会影响 data 选项中的内容如何发送到服务器。

data 选项既可以包含一个查询字符串，比如 key1=value1&key2=value2 ，也可以是一个映射，比如 {key1: 'value1', key2: 'value2'} 。如果使用了后者的形式，则数据再发送器会被转换成查询字符串。这个处理过程也可以通过设置 processData 选项为 false 来回避。如果我们希望发送一个 XML 对象给服务器时，这种处理可能并不合适。并且在这种情况下，我们也应当改变 contentType 选项的值，用其他合适的 MIME 类型来取代默认的 application/x-www-form-urlencoded 。

## 高级选项

global 选项用于阻止响应注册的回调函数，比如 .ajaxSend，或者 ajaxError，以及类似的方法。这在有些时候很有用，比如发送的请求非常频繁且简短的时候，就可以在 ajaxSend 里禁用这个。

如果服务器需要 HTTP 认证，可以使用用户名和密码可以通过 username 和 password 选项来设置。

Ajax 请求是限时的，所以错误警告被捕获并处理后，可以用来提升用户体验。请求超时这个参数通常就保留其默认值，要不就通过 jQuery.ajaxSetup 来全局设定，很少为特定的请求重新设置 timeout 选项。

默认情况下，请求总会被发出去，但浏览器有可能从它的缓存中调取数据。要禁止使用缓存的结果，可以设置 cache 参数为 false。如果希望判断数据自从上次请求后没有更改过就报告出错的话，可以设置 ifModified 为 true。

scriptCharset 允许给 <script> 标签的请求设定一个特定的字符集，用于 script 或者 jsonp 类似的数据。当脚本和页面字符集不同时，这特别好用。

Ajax 的第一个字母是 asynchronous 的开头字母，这意味着所有的操作都是并行的，完成的顺序没有前后关系。$.ajax() 的 async 参数总是设置成true，这标志着在请求开始后，其他代码依然能够执行。强烈不建议把这个选项设置成 false，这意味着所有的请求都不再是异步的了，这也会导致浏览器被锁死。

$.ajax 函数返回它创建的 XMLHttpRequest 对象。通常 jQuery 只在内部处理并创建这个对象，但用户也可以通过 xhr 选项来传递一个自己创建的 xhr 对象。返回的对象通常已经被丢弃了，但依然提供一个底层接口来观察和操控请求。比如说，调用对象上的 .abort() 可以在请求完成前挂起请求。
