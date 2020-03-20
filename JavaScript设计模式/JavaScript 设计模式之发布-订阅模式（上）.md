### 什么是发布订阅模式？

发布—订阅模式又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知。在JavaScript开发中，我们一般用事件模型来替代传统的发布—订阅模式。

### 发布订阅模式的构成

最常见的发布订阅模式就是咱们DOM事件，仔细回想一下我们要给一个按钮，绑定一个事件，当我点击按钮的时候我要让他的颜色变了，并且页面弹出一个弹出框

我们分析一下这个流程

首先，我们得知道给哪个按钮的时候绑定事件，然后我们得知道触发事件以后需要干什么？

那么在这其中谁是**发布者**？

是DOM中的按钮，因为是在它身上绑定了事件，当我们点击按钮的时候它便像订阅者发布了这个消息

那么谁是**订阅者**？

是click事件，当点击按钮时，dom发布了一条消息，而事件订阅了它，所以当它被点击的时候，订阅者会接收到消息

### 简单例子

以售楼处售楼为例子，售楼处都有一个花名册，然后当卖房的时候会给花名册里面的人依次发短信

```
var salesOffices = {}; // 定义售楼处

salesOffices.clientList = []; // 缓存列表，存放订阅者的回调函数,也就是花名册

salesOffices.listen = function( fn ){ // 增加订阅者
    this.clientList.push( fn ); // 订阅的消息添加进缓存列表，也就是把一个买房的放进花名册里面
};

salesOffices.trigger = function(){ // 发布消息
    //依次查看花名册里面的人，看他们需要什么样的房
    for( var i = 0, fn; fn = this.clientList[ i++ ]; ){
        //然后发短信告诉他们
        fn.apply( this, arguments ); // (2) // arguments 是发布消息时带上的参数
    }
};

salesOffices.listen( function( price, squareMeter ){ // 小明订阅消息即小明有一个这样买房的需求，然后我就把它添加到花名册里面去，小明没有明确的买房具体要求
    console.log( '价格= ' + price );
    console.log( 'squareMeter= ' + squareMeter );
});

//售楼处发布了一个消息，有一个88平米，售价200万的房
salesOffices.trigger( 2000000, 88 ); // 输出：200万，88平方米

//发布完以后小明就收到了消息，这就是发布-订阅模式
```

现在大家大概都有点理解了什么是发布订阅模式了吧？

如果大家再不理解的话，我再举一个通俗的例子:

> 上大学的时候甲和乙要打游戏，下午的课不准备去了，然后甲跟丙说，如果下午老师问我去哪了，你就发信息告诉我，已跟丙说，如果下午老师问我去哪了，你就发信息告诉我。然后果然不出所料，下午老师来了，并且问了甲和已去哪了？然后丙发信息给甲和已了，甲和已收到信息后干什么丙不知道，丙只是负责如果老师问了就发短信给甲和已。

这样能听明白了吗？所以发布订阅设计模式要先订阅消息，然后再等到发布消息的时候就能触发事件

上面的例子是如果只要有房卖就告诉小明，什么消息都会告诉小明

现在小明只想要80平米的房子的消息，我们来优化一下

```
var salesOffices = {}; // 定义售楼处
salesOffices.clientList = {}; // 缓存列表，存放订阅者的回调函数
salesOffices.listen = function( key, fn ){
    if ( !this.clientList[ key ] ){ // 如果还没有订阅过此类消息，给该类消息创建一个缓存列表
        this.clientList[ key ] = [];
    }
    this.clientList[ key ].push( fn ); // 订阅的消息添加进消息缓存列表
};
salesOffices.trigger = function(){ // 发布消息
    var key = Array.prototype.shift.call( arguments ), // 取出消息类型
    fns = this.clientList[ key ]; // 取出该消息对应的回调函数集合
    if ( !fns || fns.length === 0 ){ // 如果没有订阅该消息，则返回
        return false;
    }
    for( var i = 0, fn; fn = fns[ i++ ]; ){
        fn.apply( this, arguments ); // (2) // arguments是发布消息时附送的参数
    }
};
salesOffices.listen( 'squareMeter88', function( price ){ // 小明订阅88平方米房子的消息
    console.log( '价格= ' + price ); // 输出： 2000000
});

salesOffices.trigger( 'squareMeter88', 2000000 ); // 发布88平方米房子的价格,小明能收到
salesOffices.trigger( 'squareMeter110', 3000000 ); // 发布110平方米房子的价格，小明收不到
```
这就是一个比较常用的发布订阅的设计模式了，下一篇进行重点讲解，本篇先了解一下什么是发布订阅模式，也就是观察者模式

