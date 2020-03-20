**什么叫跨域？**

跨域是指一个域下的文档或脚本试图去请求另一个域下的资源，这里跨域是广义的。

广义的跨域:

1. 资源跳转： A链接、重定向、表单提交
2. 资源嵌入： <link>、<script>、<img>、<frame>等dom标签，还有样式中background:url()、@font-face()等文件外链
3. 脚本请求： js发起的ajax请求、dom和js对象的跨域操作等

**什么是同源策略？**

同源策略/SOP（Same origin policy）是一种约定，由Netscape公司1995年引入浏览器，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，浏览器很容易受到XSS、CSFR等攻击。所谓同源是指"协议+域名+端口"三者相同，即便两个不同的域名指向同一个ip地址，也非同源。

同源策略限制以下几种行为：

1. Cookie、LocalStorage 和 IndexDB 无法读取
2. DOM 和 Js对象无法获得
3. AJAX 请求不能发送

**常见的跨域场景**

![image](https://ask.qcloudimg.com/http-save/yehe-1093975/bspy4919df.jpeg?imageView2/2/w/1620)

### 跨域资源共享

CORS（Cross-Origin Resource  Sharing，跨源资源共享）是W3C的一个工作草案，定义了在必须访问跨源资源时，浏览器与服务器应该如何沟通。CORS 背后的基本思想，就是使用自定义的 HTTP 头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。

#### IE对CORS的实现

XDR（XDomainRequest）类型

这个对象与 XHR 类似，但能实现安全可靠的跨域通信。XDR 对象的安全机制部分实现了 W3C 的 CORS 规范。以下是 XDR 与 XHR 的一些不同之处。

- cookie 不会随请求发送，也不会随响应返回。
- 只能设置请求头部信息中的 Content-Type 字段。
- 不能访问响应头部信息。
- 只支持 GET 和 POST 请求。

XDR请求只能是异步请求，用法与XHR非常相似，与XHR的区别是open()方法，XDR只接收两个参数(请求类型，url)

示例：
```
var xdr = new XDomainRequest();
xdr.onload = function(){
 alert(xdr.responseText);
};
xdr.open("get", "http://www.somewhere-else.com/page/");
xdr.send(null);
```
#### 其他浏览器对CORS的实现

Firefox 3.5+、Safari 4+、Chrome、iOS 版 Safari 和 Android 平台中的 WebKit 都通过 XMLHttpRequest
对象实现了对 CORS 的原生支持。在尝试打开不同来源的资源时，无需额外编写代码就可以触发这个行
为。要请求位于另一个域中的资源，使用标准的 XHR 对象并在 open()方法中传入绝对 URL 即可，例如：

```
var xhr = createXHR();
xhr.onreadystatechange = function(){
 if (xhr.readyState == 4){
 if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
 alert(xhr.responseText);
 } else {
 alert("Request was unsuccessful: " + xhr.status);
 }
 }
};
xhr.open("get", "http://www.somewhere-else.com/page/", true);
xhr.send(null);
```
跨域 XHR 对象也有一些限制，但为了安全这些限制是必需的。以下就是这些限制。

- 不能使用 setRequestHeader()设置自定义头部。
- 不能发送和接收 cookie。
- 调用 getAllResponseHeaders()方法总会返回空字符串。

#### Preflighted Reqeusts
CORS 通过一种叫做 Preflighted Requests 的透明服务器验证机制支持开发人员使用自定义的头部、
GET 或 POST 之外的方法，以及不同类型的主体内容。在使用下列高级选项来发送请求时，就会向服务
器发送一个 Preflight 请求。这种请求使用 OPTIONS 方法，发送下列头部。

- Origin：与简单的请求相同。
- Access-Control-Request-Method：请求自身使用的方法。
- Access-Control-Request-Headers：（可选）自定义的头部信息，多个头部以逗号分隔。
以下是一个带有自定义头部 NCZ 的使用 POST 方法发送的请求。
- Access-Control-Allow-Origin：与简单的请求相同。
- Access-Control-Allow-Methods：允许的方法，多个方法以逗号分隔。
- Access-Control-Allow-Headers：允许的头部，多个头部以逗号分隔。
- Access-Control-Max-Age：应该将这个 Preflight 请求缓存多长时间（以秒表示）。

Preflight 请求结束后，结果将按照响应中指定的时间缓存起来。而为此付出的代价只是第一次发送
这种请求时会多一次 HTTP 请求。

#### 带凭据的请求
默认情况下，跨源请求不提供凭据（cookie、HTTP 认证及客户端 SSL 证明 等 ）。 通 过 将withCredentials 属性设置为true，可以指定某个请求应该发送凭据。如果服务器接受带凭据的请求，会用下面的 HTTP 头部来响应。

Access-Control-Allow-Credentials: true 

#### 其他跨域技术

##### 图像Ping 

动态创建图像经常用于图像Ping。图像Ping是与服务器进行简单、单向的跨域通信的一种方式。请求的数据是通过查询字符串形式发送的，而响应可以是任意内容，但通常是像素图或 204 响应。通过图像 Ping，浏览器得不到任何具体的数据，但通过侦听 load 和 error 事件，它能知道响应是什么时候接收到的。来看下面的例子。

```
var img = new Image();
img.onload = img.onerror = function(){
 alert("Done!");
};
img.src = "http://www.example.com/test?name=Nicholas"; 
```
图像 Ping 最常用于跟踪用户点击页面或动态广告曝光次数。图像Ping有两个主要的缺点，一是只能发送 GET请求，二是无法访问服务器的响应文本。因此，图像Ping只能用于浏览器与服务器间的单向通信。

##### JSONP

JSONP 是 JSON with padding（填充式 JSON 或参数式 JSON）的简写，是应用 JSON 的一种新方法，在后来的Web服务中非常流行。JSONP看起来与JSON差不多，只不过是被包含在函数调用中的 JSON，就像下面这样。

callback({ "name": "Nicholas" }); 

JSONP 由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数。回调函数的名字一般是在请求中指定的。而数据就是传入回调函数中的JSON数据。下面是一个典型的JSONP请求。

JSONP 是通过动态<script>元素来使用的，使用时可以为src属性指定一个跨域URL。这里的script元素与img元素类似，都有能力不受限制地从其他域加载资源。因为 JSONP 是有效的 JavaScript 代码，所以在请求完成后，即在JSONP响应加载到页面中以后，就会立即执行。

示例:

```
function handleResponse(response){
 alert("You’re at IP address " + response.ip + ", which is in " +
 response.city + ", " + response.region_name);
}
var script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild); 
```

使用JSONP时候一定要保证其它域安全可靠

#### Comet
Ajax 是一种从页面向服务器请求数据的技术，而Comet则是一种服务器向页面推送数据的技
术。Comet 能够让信息近乎实时地被推送到页面上

实现方法:

- 长轮询：传统轮询（也称为短轮询）的一个翻版，即浏览器定时向服务器发送请求，看有没有更新的数据。页面发起一个到服务器的请求，然后服务器一直保持连接打开，直到有数据可发送。发送完数据之后，浏览器关闭连接，随即又发起一个到服务器的新请求。这一过程在页面打开期间一直持续不断。
- HTTP 流：就是浏览器向服务器发送一个请求，而服务器保持连接打开，然后周
期性地向浏览器发送数据。

HTTP流示例:
```
function createStreamingClient(url, progress, finished){
     var xhr = new XMLHttpRequest(),
     received = 0;
     xhr.open("get", url, true);
     xhr.onreadystatechange = function(){
     var result;
     if (xhr.readyState == 3){
     //只取得最新数据并调整计数器
     result = xhr.responseText.substring(received);
     received += result.length;
     //调用 progress 回调函数
     progress(result);
     } else if (xhr.readyState == 4){
     finished(xhr.responseText);
     }
     };
     xhr.send(null);
     return xhr;
}
var client = createStreamingClient("streaming.php", function(data){
 alert("Received: " + data);
 }, function(data){
 alert("Done!");
 }); 
```
随着不断从服务器接收数
据，readyState 的值会周期性地变为 3。当 readyState 值变为 3 时，responseText 属性中就会保
存接收到的所有数据。

#### 服务器发送事件

SSE（Server-Sent Events，服务器发送事件）是围绕只读Comet交互推出的API或者模式。SSE API用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的 MIME类型必须是 text/event-stream，而且是浏览器中的 JavaScript API 能解析格式输出。SSE 支持短轮询、长轮询和HTTP流，而且能在断开连接时自动确定何时重新连接。有了这么简单实用的 API，再实现 Comet 就容易多了。

##### SSE API 
SSE 的 JavaScript API 与其他传递消息的 JavaScript API 很相似。要预订新的事件流，首先要创建一个新的 EventSource对象，并传进一个入口点：

var source = new EventSource("myevents.php"); 

注意，传入的 URL 必须与创建对象的页面同源（相同的URL模式、域及端口）。EventSource 的实例有一个 readyState属性，值为0表示正连接到服务器，值为1表示打开了连接，值为 2 表示关闭了连接。

还有其它三个事件
- open：在建立连接时触发。
- message：在从服务器接收到新事件时触发。
- error：在无法建立连接时触发

#####  事件流

所谓的服务器事件会通过一个持久的 HTTP 响应发送，这个响应的 MIME 类型为 text/eventstream。

#### Web Sockets 

> 由于 Web Sockets 使用了自定义的协议，所以URL模式也略有不同。未加密的连接不再是 http://，而是 ws://；加密的连接也不是 https://，而是 wss://。在使用 Web Socket URL 时，必须带着这个模式，因为将来还有可能支持其他模式。

> 使用自定义协议而非 HTTP协议的好处是，能够在客户端和服务器之间发送非常少量的数据，而不必担心 HTTP 那样字节级的开销。由于传递的数据包很小，因此 Web Sockets 非常适合移动应用。

##### Web Sockets API 

```
//创建连接
var socket = new WebSocket("ws://www.example.com/server.php"); 
```

实例化了 WebSocket 对象后，浏览器就会马上尝试创建连接。与 XHR 类似，WebSocket 也有一个表示当前状态的 readyState 属性。不过，这个属性的值与 XHR 并不相同，而是如下所示。

- WebSocket.OPENING (0)：正在建立连接。
- WebSocket.OPEN (1)：已经建立连接。
- WebSocket.CLOSING (2)：正在关闭连接。
- WebSocket.CLOSE (3)：已经关闭连接。


关闭Web Socket
```
socket.close();
```

发送和接收数据

```
var socket = new WebSocket("ws://www.example.com/server.php");
socket.send("Hello world!");

```
只能发送字符串，所以如果需要发送对象数据，需要将数据序列化为一个 JSON 字符串


##### 其他事件
- open：在成功建立连接时触发。
- error：在发生错误时触发，连接不能持续。
- close：在连接关闭时触发。



关于跨域这个老生常谈的问题可以看看这篇文章：[什么是跨域？解决方案有哪些？](https://cloud.tencent.com/developer/article/1175899)

也可以利用一些小插件，比如谷歌的Moesif Orign & CORS Changer扩展应用等