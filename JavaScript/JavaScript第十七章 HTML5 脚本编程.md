### 跨文档消息传递
> 跨文档消息传送（cross-document messaging），有时候简称为 XDM，指的是在来自不同域的页面间
传递消息。例如，www.wrox.com 域中的页面与位于一个内嵌框架中的 p2p.wrox.com 域中的页面通信。

- postMessage():接收两个参数：一条消息和一个表示消息接收方来自哪个域的字符串。

> 值得注意:postMessage()向另一个地方传递数据。对于 XDM 而言，“另一个地方”指的
是包含在当前页面中的<iframe>元素，或者由当前页面弹出的窗口。

##### 如何接收？
> 接收到 XDM 消息时，会触发 window 对象的 message 事件。传递给 onmessage 处理程序的事件对象包含以下三方面的重要信息。
- data：作为 postMessage()第一个参数传入的字符串数据。(最好是字符串)
- origin：发送消息的文档所在的域，例如"http://www.wrox.com"。
- source：发送消息的文档的 window 对象的代理。这个代理对象主要用于在发送上一条消息的
窗口中调用 postMessage()方法。如果发送消息的窗口来自同一个域，那这个对象就是
window。

### 原生拖放
> 最早引入的是IE4，只能拖放img和某些文本

#### 拖放事件
> 通过拖放事件，可以控制拖放相关的各个方面。其中最关键的地方在于确定哪里发生了拖放事件，
有些事件是在被拖动的元素上触发的，而有些事件是在放置目标上触发的。拖动某元素时，将依次触发
下列事件：
1. dragstart
2. drag
3. dragend

> 当某个元素被拖动到一个有效的放置目标上时，下列事件会依次发生
1. dragenter
2. dragover
3. dragleave 或 drop

> 只要有元素被拖动到放置目标上，就会触发 dragenter 事件（类似于 mouseover 事件）。紧随其
后的是 dragover 事件，而且在被拖动的元素还在放置目标的范围内移动时，就会持续触发该事件。如
果元素被拖出了放置目标，dragover 事件不再发生，但会触发 dragleave 事件（类似于 mouseout
事件）。如果元素被放到了放置目标中，则会触发 drop 事件而不是 dragleave 事件。上述三个事件的
目标都是作为放置目标的元素。

#### 自定义放置目标
> 阻止拖放的默认事件，即可实现自定义

```
var droptarget = document.getElementById("droptarget");
EventUtil.addHandler(droptarget, "dragover", function(event){
 EventUtil.preventDefault(event);
});
EventUtil.addHandler(droptarget, "dragenter", function(event){
 EventUtil.preventDefault(event);
}); 

EventUtil.addHandler(droptarget, "drop", function(event){
 EventUtil.preventDefault(event);
}); 

```
#### dataTransfer对象
> 是事件对象的一个属性，用于从被拖动元素向放置目标传递字符串格式的数据。

方法:
- setData()：设置数据

示例:
```
//设置文本数据
event.dataTransfer.setData("text", "some text");
//设置 URL
event.dataTransfer.setData("URL", "http://www.wrox.com/");

```

- getData()：获取数据

示例:
```
var text = event.dataTransfer.getData("text"); 
var url = event.dataTransfer.getData("URL");
```

属性:

- dropEffect：属性可以知道被拖动的元素能够执行哪种放置行为
    - "none"：不能把拖动的元素放在这里。这是除文本框之外所有元素的默认值。
    - "move"：应该把拖动的元素移动到放置目标。
    - "copy"：应该把拖动的元素复制到放置目标。
    - "link"：表示放置目标会打开拖动的元素（但拖动的元素必须是一个链接，有 URL）。
> 注意："link"：要使用 dropEffect
属性，必须在 ondragenter 事件处理程序中针对放置目标来设置它。


- effectAllowed：表示允许拖动元
素的哪种 dropEffect，effectAllowed 属性可能的值如下。
    - "uninitialized"：没有给被拖动的元素设置任何放置行为。
    - "none"：被拖动的元素不能有任何行为。
    - "copy"：只允许值为"copy"的 dropEffect。
    - "link"：只允许值为"link"的 dropEffect。
    - "move"：只允许值为"move"的 dropEffect。
    - "copyLink"：允许值为"copy"和"link"的 dropEffect。
    - "copyMove"：允许值为"copy"和"move"的 dropEffect。
    - "linkMove"：允许值为"link"和"move"的 dropEffect。
    - "all"：允许任意 dropEffect。

> 注意：必须在 ondragstart 事件处理程序中设置 effectAllowed 属性。

#### 可拖动
> 设置元素draggable为true即可变成可拖动元素

#### 媒体元素
> "audio"和"video"两个标签

属性列表

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191107213908703.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3OTM4OTEw,size_16,color_FFFFFF,t_70)

事件列表

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191107213841897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3OTM4OTEw,size_16,color_FFFFFF,t_70)
#### 检测解码器支持情况
> canPlayType(),该方法接收一种格式/编解码器字符串，返回
"probably"、"maybe"或""（ 空字符串）。

示例:
```
var audio = document.getElementById("audio-player");
//很可能"maybe"
if (audio.canPlayType("audio/mpeg")){
 //进一步处理
}
//可能是"probably"
if (audio.canPlayType("audio/ogg; codecs=\"vorbis\"")){
 //进一步处理
}
```

#### 历史状态管理器
> 历史状态管理是现代 Web 应用开发中的一个难点。在现代 Web 应用中，用户的每次操作不一定会
打开一个全新的页面，因此“后退”和“前进”按钮也就失去了作用，导致用户很难在不同状态间切换。
要解决这个问题，首选使用 hashchange 事件。HTML5 通过更新 history 对象为
管理历史状态提供了方便。

> 通过 hashchange 事件，可以知道 URL 的参数什么时候发生了变化，即什么时候该有所反应。而
通过状态管理 API ，能够在不加载新页面的情况下改变浏览器的 URL 。为此，需要使用
history.pushState()方法，该方法可以接收三个参数：状态对象、新状态的标题和可选的相对 URL。

示例：
```
history.pushState({name:"Nicholas"}, "Nicholas' page", "nicholas.html"); 

```
> 执行 pushState()方法后，新的状态信息就会被加入历史状态栈，而浏览器地址栏也会变成新的
相对 URL。但是，浏览器并不会真的向服务器发送请求，即使状态改变之后查询 location.href 也会
返回与地址栏中相同的地址。另外，第二个参数目前还没有浏览器实现，因此完全可以只传入一个空字
符串，或者一个短标题也可以。而第一个参数则应该尽可能提供初始化页面状态所需的各种信息。

> 因为 pushState()会创建新的历史状态，所以你会发现“后退”按钮也能使用了。按下“后退”
按钮，会触发 window 对象的 popstate 事件①。popstate 事件的事件对象有一个 state 属性，这个
属性就包含着当初以第一个参数传递给 pushState()的状态对象。

```
EventUtil.addHandler(window, "popstate", function(event){
 var state = event.state;
 if (state){ //第一个页面加载时 state 为空
 processState(state);
 }
}); 
```

> 注意：得到这个状态对象后，必须把页面重置为状态对象中的数据表示的状态（因为浏览器不会自动为你
做这些）。记住，浏览器加载的第一个页面没有状态，因此单击“后退”按钮返回浏览器加载的第一个
页面时，event.state 值为 null。要更新当前状态，可以调用 replaceState()，传入的参数与 pushState()的前两个参数相同。调用这个方法不会在历史状态栈中创建新状态，只会重写当前状态。