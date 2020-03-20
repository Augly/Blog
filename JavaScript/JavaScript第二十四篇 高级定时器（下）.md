### 数组分块
所谓数组分块，就是当你发现某个循环占用了大量时间，同时对于上述两个问题，你的回答都是“否”，那么你就可以使用定时器分割这个循环。

思路是结合定时器进行递归调用定时器

基本示例如下:

```
setTimeout(function(){
     //取出下一个条目并处理
     var item = array.shift();
     process(item);
     //若还有条目，再设置另一个定时器
     if(array.length > 0){
        setTimeout(arguments.callee, 100);
     }
}, 100);

```
封装成函数如下:

```
function chunk(array, process, context){
     setTimeout(function(){
         var item = array.shift();
         process.call(context, item);
         if (array.length > 0){
             setTimeout(arguments.callee, 100);
         }
     }, 100);
} 
```

> chunk()方法接受三个参数：要处理的项目的数组，用于处理项目的函数，以及可选的运行该函数的环境。函数内部用了之前描述过的基本模式，通过call()调用的process()函数，这样可以设置一个合适的执行环境（如果必须）。定时器的时间间隔设置为了 100ms，使得 JavaScript进程有时间在处理项目的事件之间转入空闲。你可以根据你的需要更改这个间隔大小，不过100ms在大多数情况下效果不错。可以按如下所示使用该函数：

```
var data = [12,123,1234,453,436,23,23,5,4123,45,346,5634,2234,345,342];
function printValue(item){
 var div = document.getElementById("myDiv");
 div.innerHTML += item + "<br>";
}
chunk(data, printValue);
```
什么场景用？

**数组分块** 一般用于什么场景呢？一般用于耗时比较长的循环时，以防脚本长时间运行，导致浏览器崩溃

还有一种方法是吧数组以8为基数，0-8为一组，9-16为一组，以此类推,然后循环，这里里面的8个依次调用，只需要循环外面的大块，也能进行优化

### 函数节流

什么是函数节流？

某些代码不可以在没有间断的情况连续重复执行。第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码。当第二次调用该函数时，它会清除前一次的定时器并设置另一个。如果前一个定时器已经执行过了，这个操作就没有任何意义。然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器。目的是只有在执行函数的请求停止了一段时间之后才执行。

函数节流的基本模式如下:

```
var processor = {
     timeoutId: null,
     //实际进行处理的方法
     performProcessing: function(){
        //实际执行的代码
     },
     //初始处理调用的方法
     process: function(){
         clearTimeout(this.timeoutId);
         var that = this;
         this.timeoutId = setTimeout(function(){
            that.performProcessing();
         }, 100);
     }
};
//尝试开始执行
processor.process();

```

简化以后:

```
function throttle(method, context) {
     clearTimeout(method.tId);
     method.tId= setTimeout(function(){
        method.call(context);
     }, 100);
} 

```

函数节流的作用是在高频率得调用一个函数时，最终只会在停止触发后调用一次函数，能节省操作成本，和优化性能

### 自定义事件

事件，什么是事件？

事件是一种叫做观察者的设计模式，这是一种创建松散耦合代码的技术。对象可以发布事件，用来表示在该对象生命周期中某个有趣的时刻到了。然后其他对象可以观察该对象，等待这些有趣的时刻到来并通过运行代码来响应。

观察者模式：

观察者模式由两类对象组成：主体和观察者。主体负责发布事件，同时观察者通过订阅这些事件来观察该主体。该模式的一个关键概念是主体并不知道观察者的任何事情，也就是说它可以独自存在并正常运作即使观察者不存在。从另一方面来说，观察者知道主体并能注册事件的回调函数（事件处理程序）。涉及DOM上时，DOM元素便是主体，你的事件处理代码便是观察者。

事件是与 DOM 交互的最常见的方式，但它们也可以用于非 DOM 代码中——通过实现自定义事件。
自定义事件背后的概念是创建一个管理事件的对象，让其他对象监听那些事件。实现此功能的基本模式
可以如下定义：

```
function EventTarget(){
    this.handlers = {};
}
EventTarget.prototype = {
     constructor: EventTarget,
     addHandler: function(type, handler){
         if (typeof this.handlers[type] == "undefined"){
            this.handlers[type] = [];
         }
         this.handlers[type].push(handler);
     },
     fire: function(event){
         if (!event.target){
            event.target = this;
         }
         if (this.handlers[event.type] instanceof Array){
             var handlers = this.handlers[event.type];
             for (var i=0, len=handlers.length; i < len; i++){
                handlers[i](event);
             }
         }
     },
     removeHandler: function(type, handler){
         if (this.handlers[type] instanceof Array){
             var handlers = this.handlers[type];
             for (var i=0, len=handlers.length; i < len; i++){
                 if (handlers[i] === handler){
                     break;
                 }
             }
             handlers.splice(i, 1); 
         }
     }
};

```
> EventTarget 类型有一个单独的属性handlers，用于储存事件处理程序。还有三个方法：addHandler() ，用于注册给定类型事件的事件处理程序；fire()，用于触发一个事件；removeHandler()，用于注销某个事件类型的事件处理程序。

>   addHandler()方法接受两个参数：事件类型和用于处理该事件的函数。当调用该方法时，会进行一次检查，看看handlers属性中是否已经存在一个针对该事件类型的数组；如果没有，则创建一个新的。然后使用 push()将该处理程序添加到数组的末尾。

> 如果要触发一个事件，要调用fire()函数。该方法接受一个单独的参数，是一个至少包含 type属性的对象。fire()方法先给event对象设置一个target属性，如果它尚未被指定的话。然后它就查找对应该事件类型的一组处理程序，调用各个函数，并给出 event 对象。因为这些都是自定义事件，所以event对象上还需要的额外信息由你自己决定。

> removeHandler()方法是addHandler()的辅助，它们接受的参数一样：事件的类型和事件处理程序。这个方法搜索事件处理程序的数组找到要删除的处理程序的位置。如果找到了，则使用 break操作符退出for循环。然后使用splice()方法将该项目从数组中删除。

使用 EventTarget 类型的自定义事件可以如下使用：

```
function handleMessage(event){
 alert("Message received: " + event.message);
}
//创建一个新对象
var target = new EventTarget();
//添加一个事件处理程序
target.addHandler("message", handleMessage);
//触发事件
target.fire({ type: "message", message: "Hello world!"});
//删除事件处理程序
target.removeHandler("message", handleMessage);
//再次，应没有处理程序
target.fire({ type: "message", message: "Hello world!"}); 

```
