#### FormData

FormData 为序列化表单以及创建与表单格式相同的数据（用于通过 XHR 传输）提供
了便利。

用法如下:
```
var data = new FormData();
data.append("name", "Nicholas"); 
```
这个 append()方法接收两个参数：键和值，分别对应表单字段的名字和字段中包含的值。
创建了 FormData 的实例后，可以将它直接传给 XHR 的 send()方法

即:
```
xhr.send(data);
```
#### 超时设定

什么时候会用到超时呢？
我们假设一个场景，你很开心的对着接口，突然网络原因变差了，你等待一个接口的数据响应足足等了20多秒

这时候超时这个概念就出来了

超时设定：如果在规定的时间内浏览器还没有接收到响应，那么就会触发 timeout 事
件，进而会调用 ontimeout 事件处理程序。

示例:
```
var xhr = createXHR();
xhr.onreadystatechange = function(){
     if (xhr.readyState == 4){
         try {
             if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
                 alert(xhr.responseText);
             } else {
                 alert("Request was unsuccessful: " + xhr.status);
             }
         } catch (ex){
         //假设由 ontimeout 事件处理程序处理
         }
     }
};
xhr.open("get", "timeout.php", true);
xhr.timeout = 1000; //将超时设置为 1 秒钟（仅适用于 IE8+）
xhr.ontimeout = function(){
 //超时之后触发
 alert("Request did not return in a second.");
};
xhr.send(null); 
```
以上就是超时设定的用法

#### overrideMimeType()方法
作用：重写 XHR 响应的 MIME 类型。支持 overrideMimeType()方法的浏览器有 Firefox、Safari 4+、Opera 10.5 和 Chrome。

### 进度事件
这个是重点！！！

> 进度事件经常用来检测文件的上传和下载进度

有以下 6 个进度事件：
- loadstart：在接收到响应数据的第一个字节时触发。
- progress：在接收响应期间持续不断地触发。
- error：在请求发生错误时触发。
- abort：在因为调用 abort()方法而终止连接时触发。
- load：在接收到完整的响应数据时触发。
- loadend：在通信完成或者触发 error、abort 或 load 事件后触发。

每个请求都从触发 loadstart事件开始，接下来是一或多个progress事件，然后触发error、abort 或 load 事件中的一个，最后以触发 loadend 事件结束。

支持前 5 个事件的浏览器有 Firefox 3.5+、Safari 4+、Chrome、iOS 版 Safari 和 Android 版 WebKit。
Opera（从第 11 版开始）、IE 8+只支持 load 事件。目前还没有浏览器支持 loadend 事件。

#### load事件
> 当浏览器接收到服务器的响应时会触发load事件

示例:
```
var xhr = createXHR();
xhr.onload = function(){
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
        alert(xhr.responseText);
    } else {
        alert("Request was unsuccessful: " + xhr.status);
    }
};
xhr.open("get", "altevents.php", true);
xhr.send(null); 
```
只要浏览器接收到服务器的响应，不管其状态如何，都会触发load事件。而这意味着你必须要检查 status 属性，才能确定数据是否真的已经可用了。

#### progress事件
> 会在浏览器接收新数据期间周期性地触发。而onprogress事件处理程序会接收到一个 event 对象，其 target属性是XHR对象，但包含着三个额外的属性：lengthComputable、position 和 totalSize。其中，lengthComputable是一个表示进度信息是否可用的布尔值，position 表示已经接收的字节数，totalSize 表示根据Content-Length 响应头部确定的预期字节数。有了这些信息，我们就可以为用户创建一个进度指示器
了。

示例:
```
var xhr = createXHR();
xhr.onload = function(event){
 if ((xhr.status >= 200 && xhr.status < 300) ||
 xhr.status == 304){
 alert(xhr.responseText);
 } else {
 alert("Request was unsuccessful: " + xhr.status);
 }
};
xhr.onprogress = function(event){
 var divStatus = document.getElementById("status");
 if (event.lengthComputable){
 divStatus.innerHTML = "Received " + event.position + " of " +
 event.totalSize +" bytes";
 }
};
xhr.open("get", "altevents.php", true);
xhr.send(null); 
```
为确保正常执行，必须在调用 open()方法之前添加 onprogress 事件处理程序。

每次触发 progress 事件，都会以新的状态信息更新 HTML 元素的内容。如果响应头部中包含
Content-Length 字段，那么也可以利用此信息来计算从响应中已经接收到的数据的百分比。