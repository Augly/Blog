> Ajax 技术的核心是 XMLHttpRequest 对象（简称 XHR），这是由微软首先引入的一个特性，其他
浏览器提供商后来都提供了相同的实现。这种技术就是无须刷新页面即可从服务器取得数
据，但不一定是 XML 数据。

### XMLHttpRequest 对象

最早是在IE5中被引入的，在 IE 中可能会遇到三种不同版本的 XHR 对象，即 MSXML2.XMLHttp、
MSXML2.XMLHttp.3.0 和 MXSML2.XMLHttp.6.0。

兼容IE的写法:

```
function createXHR(){
    if (typeof XMLHttpRequest != "undefined"){
        return new XMLHttpRequest();
    } else if (typeof ActiveXObject != "undefined"){
         if (typeof arguments.callee.activeXString != "string"){
            var versions = [ "MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0","MSXML2.XMLHttp"],i, len;
                for (i=0,len=versions.length; i < len; i++){
                 try {
                     new ActiveXObject(versions[i]);
                     arguments.callee.activeXString = versions[i];
                     break;
                 } catch (ex){
                 //跳过
                 }
                }
            }
         return new ActiveXObject(arguments.callee.activeXString);
    } else {
            throw new Error("No XHR object available.");
    }
} 

```
这个函数会尽力根据 IE 中可用的 MSXML 库的情况创建最新版本的 XHR 对象。

> 这个函数中新增的代码首先检测原生 XHR 对象是否存在，如果存在则返回它的新实例。如果原生
对象不存在，则检测 ActiveX 对象。如果这两种对象都不存在，就抛出一个错误。然后，就可以使用下
面的代码在所有浏览器中创建 XHR 对象了。

```
var xhr = createXHR(); 
```
#### XHR的用法

在使用 XHR 对象时，要调用的第一个方法是 open()，它接受 3 个参数：要发送的请求的类型
（"get"、"post"等）、请求的 URL 和表示是否异步发送请求的布尔值。

示例:
```
xhr.open("get", "example.php", false); 
```

> 需要说明两点：一是 URL
相对于执行代码的当前页面（当然也可以使用绝对路径）；二是调用 open()方法并不会真正发送请求，
而只是启动一个请求以备发送。

如何发送数据？

> 调用send()，send()方法接收一个参数，即要作为请求主体发送的数据。如果不需要通过请求主体发送
数据，则必须传入 null，因为这个参数对有些浏览器来说是必需的。调用 send()之后，请求就会被分
派到服务器。由于这次请求是同步的，JavaScript 代码会等到服务器响应之后再继续执行。

返回值主体如下:

- responseText：作为响应主体被返回的文本。
- responseXML：如果响应的内容类型是"text/xml"或"application/xml"，这个属性中将保存包含着响应数据的 XML DOM 文档。
- status：响应的 HTTP 状态。
- statusText：HTTP 状态的说明。

在接收到响应后，第一步是检查 status 属性，以确定响应已经成功返回。一般来说，可以将 HTTP状态代码为 200 作为成功的标志。此时，responseText 属性的内容已经就绪，而且在内容类型正确的情况下，responseXML 也应该能够访问了。此外，状态代码为 304 表示请求的资源并没有被修改，可以直接使用浏览器中缓存的版本；当然，也意味着响应是有效的。为确保接收到适当的响应，应该像下面这样检查上述这两种状态代码：

```
xhr.open("get", "example.txt", false);
xhr.send(null);
if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
 alert(xhr.responseText);
} else {
 alert("Request was unsuccessful: " + xhr.status);
} 
```

异步请求需要注意的地方
可以检测 XHR 对象的 readyState 属性，该属性表示请求响应过程的当前活动阶段。这个属性可取的值如下。
- 0：未初始化。尚未调用 open()方法。
- 1：启动。已经调用 open()方法，但尚未调用 send()方法。
- 2：发送。已经调用 send()方法，但尚未接收到响应。
- 3：接收。已经接收到部分响应数据。
- 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

> 只要 readyState 属性的值由一个值变成另一个值，都会触发一次readystatechange事件。可以利用这个事件来检测每次状态变化后readyState的值。通常，我们只对readyState值为4的阶段感兴趣，因为这时所有数据都已经就绪。不过，必须在调用open()之前指定onreadystatechange事件处理程序才能确保跨浏览器兼容性。

示例:
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
xhr.open("get", "example.txt", true);
xhr.send(null); 
```

#### HTTP头部信息

每个 HTTP 请求和响应都会带有相应的头部信息，其中有的对开发人员有用，有的也没有什么用。
XHR 对象也提供了操作这两种头部（即请求头部和响应头部）信息的方法。

  每个 HTTP 请求和响应都会带有相应的头部信息，其中有的对开发人员有用，有的也没有什么用。
XHR 对象也提供了操作这两种头部（即请求头部和响应头部）信息的方法。

- Accept：浏览器能够处理的内容类型。
- Accept-Charset：浏览器能够显示的字符集。
- Accept-Encoding：浏览器能够处理的压缩编码。
- Accept-Language：浏览器当前设置的语言。
- Connection：浏览器与服务器之间连接的类型。
- Cookie：当前页面设置的任何 Cookie。
- Host：发出请求的页面所在的域 。
- Referer：发出请求的页面的 URI。注意，HTTP规范将这个头部字段拼写错了，而为保证与规范一致，也只能将错就错了。（这个英文单词的正确拼法应该是 referrer。）
- User-Agent：浏览器的用户代理字符串。

> 虽然不同浏览器实际发送的头部信息会有所不同，但以上列出的基本上是所有浏览器都会发送的。使用 setRequestHeader()方法可以设置自定义的请求头部信息。这个方法接受两个参数：头部字段的名称和头部字段的值。要成功发送请求头部信息，必须在调用 open()方法之后且调用 send()方法之前调用 setRequestHeader()，如下面的例子所示。

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
xhr.open("get", "example.php", true);
//添加请求头部信息字段
xhr.setRequestHeader("MyHeader", "MyValue");
//读取请求头部信息中"MyHeader"的值
xhr.getResponseHeader("MyHeader")
xhr.send(null); 
```

#### GET请求
GET 是最常见的请求类型，最常用于向服务器查询某些信息。必要时，可以将查询字符串参数追加到 URL 的末尾，以便将信息发送给服务器。对 XHR 而言，位于传入 open()方法的 URL 末尾的查询字符串必须经过正确的编码才行。

使用 GET 请求经常会发生的一个错误，就是查询字符串的格式有问题。查询字符串中每个参数的名称和值都必须使用 encodeURIComponent()进行编码，然后才能放到 URL 的末尾；而且所有名-值对儿都必须由和号（&）分隔，如下面的例子所示。

```
xhr.open("get", "example.php?name1=value1&name2=value2", true);
下面这个函数可以辅助向现有 URL 的末尾添加查询字符串参数：
function addURLParam(url, name, value) {
     url += (url.indexOf("?") == -1 ? "?" : "&");
     url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
 return url;
} 
```
> 这个 addURLParam()函数接受三个参数：要添加参数的 URL、参数的名称和参数的值。这个函数首先检查 URL 是否包含问号（以确定是否已经有参数存在）。如果没有，就添加一个问号；否则，就添加一个和号。然后，将参数名称和值进行编码，再添加到 URL 的末尾。最后返回添加参数之后的 URL。

####  POST请求

xhr.open("post", "example.php", true); 

发送 POST 请求的第二步就是向 send()方法中传入某些数据。由于 XHR 最初的设计主要是为了处
理 XML，因此可以在此传入 XML DOM 文档，传入的文档经序列化之后将作为请求主体被提交到服务
器。当然，也可以在此传入任何想发送到服务器的字符串。

默认情况下，服务器对 POST 请求和提交 Web 表单的请求并不会一视同仁。因此，服务器端必须有程序来读取发送过来的原始数据，并从中解析出有用的部分。不过，我们可以使用 XHR 来模仿表单提交：首先将 Content-Type 头部信息设置为 application/x-www-form-urlencoded，也就是表单提交时的内容类型，其次是以适当的格式创建一个字符串。第 14 章曾经讨论过，POST 数据的格式与查询字符串格式相同。

post发送数据序列化处理示例：
```
function submitData(){
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
     xhr.open("post", "postexample.php", true);
     xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
     var form = document.getElementById("user-info");
     xhr.send(serialize(form));
} 
```

> Ajax 技术的核心是 XMLHttpRequest 对象（简称 XHR），这是由微软首先引入的一个特性，其他浏览器提供商后来都提供了相同的实现。这种技术就是无须刷新页面即可从服务器取得数据，但不一定是 XML 数据。

### XMLHttpRequest 对象

最早是在IE5中被引入的，在 IE 中可能会遇到三种不同版本的 XHR 对象，即 MSXML2.XMLHttp、
MSXML2.XMLHttp.3.0 和 MXSML2.XMLHttp.6.0。

兼容IE的写法:

```
function createXHR(){
    if (typeof XMLHttpRequest != "undefined"){
        return new XMLHttpRequest();
    } else if (typeof ActiveXObject != "undefined"){
         if (typeof arguments.callee.activeXString != "string"){
            var versions = [ "MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0","MSXML2.XMLHttp"],i, len;
                for (i=0,len=versions.length; i < len; i++){
                 try {
                     new ActiveXObject(versions[i]);
                     arguments.callee.activeXString = versions[i];
                     break;
                 } catch (ex){
                 //跳过
                 }
                }
            }
         return new ActiveXObject(arguments.callee.activeXString);
    } else {
            throw new Error("No XHR object available.");
    }
} 

```
这个函数会尽力根据 IE 中可用的 MSXML 库的情况创建最新版本的 XHR 对象。

> 这个函数中新增的代码首先检测原生 XHR 对象是否存在，如果存在则返回它的新实例。如果原生对象不存在，则检测 ActiveX 对象。如果这两种对象都不存在，就抛出一个错误。然后，就可以使用下面的代码在所有浏览器中创建 XHR 对象了。

```
var xhr = createXHR(); 
```
#### XHR的用法

在使用 XHR 对象时，要调用的第一个方法是 open()，它接受 3 个参数：要发送的请求的类型
（"get"、"post"等）、请求的 URL 和表示是否异步发送请求的布尔值。

示例:
```
xhr.open("get", "example.php", false); 
```

> 需要说明两点：一是 URL
相对于执行代码的当前页面（当然也可以使用绝对路径）；二是调用 open()方法并不会真正发送请求，而只是启动一个请求以备发送。

如何发送数据？

> 调用send()，send()方法接收一个参数，即要作为请求主体发送的数据。如果不需要通过请求主体发送数据，则必须传入 null，因为这个参数对有些浏览器来说是必需的。调用 send()之后，请求就会被分派到服务器。由于这次请求是同步的，JavaScript 代码会等到服务器响应之后再继续执行。

返回值主体如下:

- responseText：作为响应主体被返回的文本。
- responseXML：如果响应的内容类型是"text/xml"或"application/xml"，这个属性中将保存包含着响应数据的 XML DOM 文档。
- status：响应的 HTTP 状态。
- statusText：HTTP 状态的说明。

在接收到响应后，第一步是检查 status 属性，以确定响应已经成功返回。一般来说，可以将 HTTP
状态代码为 200 作为成功的标志。此时，responseText 属性的内容已经就绪，而且在内容类型正确的情况下，responseXML 也应该能够访问了。此外，状态代码为 304 表示请求的资源并没有被修改，可以直接使用浏览器中缓存的版本；当然，也意味着响应是有效的。为确保接收到适当的响应，应该像下面这样检查上述这两种状态代码：

```
xhr.open("get", "example.txt", false);
xhr.send(null);
if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
 alert(xhr.responseText);
} else {
 alert("Request was unsuccessful: " + xhr.status);
} 
```

异步请求需要注意的地方
可以检测 XHR 对象的 readyState 属性，该属性表示请求响应过程的当前活动阶段。这个属性可取的值如下。
- 0：未初始化。尚未调用 open()方法。
- 1：启动。已经调用 open()方法，但尚未调用 send()方法。
- 2：发送。已经调用 send()方法，但尚未接收到响应。
- 3：接收。已经接收到部分响应数据。
- 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

> 只要 readyState 属性的值由一个值变成另一个值，都会触发一次readystatechange事件。可以利用这个事件来检测每次状态变化后readyState的值。通常，我们只对readyState值为4的阶段感兴趣，因为这时所有数据都已经就绪。不过，必须在调用open()之前指定onreadystatechange事件处理程序才能确保跨浏览器兼容性。

示例:
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
xhr.open("get", "example.txt", true);
xhr.send(null); 
```

#### HTTP头部信息

每个 HTTP 请求和响应都会带有相应的头部信息，其中有的对开发人员有用，有的也没有什么用。
XHR 对象也提供了操作这两种头部（即请求头部和响应头部）信息的方法。

每个 HTTP 请求和响应都会带有相应的头部信息，其中有的对开发人员有用，有的也没有什么用。
XHR 对象也提供了操作这两种头部（即请求头部和响应头部）信息的方法。

- Accept：浏览器能够处理的内容类型。
- Accept-Charset：浏览器能够显示的字符集。
- Accept-Encoding：浏览器能够处理的压缩编码。
- Accept-Language：浏览器当前设置的语言。
- Connection：浏览器与服务器之间连接的类型。
- Cookie：当前页面设置的任何 Cookie。
- Host：发出请求的页面所在的域 。
- Referer：发出请求的页面的 URI。注意，HTTP规范将这个头部字段拼写错了，而为保证与规范一致，也只能将错就错了。（这个英文单词的正确拼法应该是 referrer。）
- User-Agent：浏览器的用户代理字符串。

> 虽然不同浏览器实际发送的头部信息会有所不同，但以上列出的基本上是所有浏览器都会发送的。使用 setRequestHeader()方法可以设置自定义的请求头部信息。这个方法接受两个参数：头部字段的名称和头部字段的值。要成功发送请求头部信息，必须在调用 open()方法之后且调用 send()方法之前调用 setRequestHeader()，如下面的例子所示。

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
xhr.open("get", "example.php", true);
//添加请求头部信息字段
xhr.setRequestHeader("MyHeader", "MyValue");
//读取请求头部信息中"MyHeader"的值
xhr.getResponseHeader("MyHeader")
xhr.send(null); 
```

#### GET请求
GET 是最常见的请求类型，最常用于向服务器查询某些信息。必要时，可以将查询字符串参数追加到 URL 的末尾，以便将信息发送给服务器。对 XHR 而言，位于传入 open()方法的 URL 末尾的查询字符串必须经过正确的编码才行。

使用 GET 请求经常会发生的一个错误，就是查询字符串的格式有问题。查询字符串中每个参数的名称和值都必须使用 encodeURIComponent()进行编码，然后才能放到 URL 的末尾；而且所有名-值对儿都必须由和号（&）分隔，如下面的例子所示。

```
xhr.open("get", "example.php?name1=value1&name2=value2", true);
下面这个函数可以辅助向现有 URL 的末尾添加查询字符串参数：
function addURLParam(url, name, value) {
     url += (url.indexOf("?") == -1 ? "?" : "&");
     url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
 return url;
} 
```
> 这个 addURLParam()函数接受三个参数：要添加参数的 URL、参数的名称和参数的值。这个函数
首先检查 URL 是否包含问号（以确定是否已经有参数存在）。如果没有，就添加一个问号；否则，就添
加一个和号。然后，将参数名称和值进行编码，再添加到 URL 的末尾。最后返回添加参数之后的 URL。

####  POST请求

xhr.open("post", "example.php", true); 

发送 POST 请求的第二步就是向 send()方法中传入某些数据。由于 XHR 最初的设计主要是为了处
理 XML，因此可以在此传入 XML DOM 文档，传入的文档经序列化之后将作为请求主体被提交到服务
器。当然，也可以在此传入任何想发送到服务器的字符串。

默认情况下，服务器对 POST 请求和提交 Web 表单的请求并不会一视同仁。因此，服务器端必须有程序来读取发送过来的原始数据，并从中解析出有用的部分。不过，我们可以使用 XHR 来模仿表单提交：首先将 Content-Type 头部信息设置为 application/x-www-form-urlencoded，也就是表单提交时的内容类型，其次是以适当的格式创建一个字符串。第 14 章曾经讨论过，POST 数据的格式与查询字符串格式相同。

post发送数据序列化处理示例：
```
function submitData(){
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
     xhr.open("post", "postexample.php", true);
     xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
     var form = document.getElementById("user-info");
     xhr.send(serialize(form));
} 
```

