什么是代理模式？

首先我们先看一个有趣的例子

在四月一个晴朗的早晨，小明遇见了他的百分百女孩，我们暂且称呼小明的女神为A。两天之后，小明决定给A送一束花来表白。刚好小明打听到A和他有一个共同的朋友B，于是内向的小明决定让B来代替自己完成送花这件事情。虽然小明的故事必然以悲剧收场，因为追MM更好的方式是送一辆宝马。不管怎样，我们还是先用代码来描述一下小明追女神的过程，先看看不用代理模式的情况：

```
// 首先的有花的实例
var Flower = function(){};
var xiaoming = {
    // 送花的方法，参数为送花目标
    sendFlower: function( target ){
        // 创建一个花的实例
        var flower = new Flower();
        // 送给目标
        target.receiveFlower( flower );
    }
};
// 女神
var A = {
    // 女神接收到了花
    receiveFlower: function( flower ){
        console.log( '收到花 ' + flower );
    }
};
// 小明开始送花
xiaoming.sendFlower( A );

```
接下来，我们引入代理B，即小明通过B来给A送花：

```
// 首先的有花的实例
var Flower = function(){};
var xiaoming = {
    // 送花的方法，参数为送花目标
    sendFlower: function( target ){
        // 创建一个花的实例
        var flower = new Flower();
        // 送给目标
        target.receiveFlower( flower );
    }
};
// 女神闺蜜
var B = {
    // 小明把花送给了女神闺蜜，由他转送
    receiveFlower: function( flower ){
        // 女神闺蜜把花送给了女神
        A.receiveFlower(flower)
    }
};
// 女神
var A = {
    // 女神接收到了花
    receiveFlower: function( flower ){
        console.log( '收到花 ' + flower );
    }
};
// 小明开始送花
xiaoming.sendFlower( A );
```
很显然，执行结果跟第一段代码一致，至此我们就完成了一个最简单的代理模式的编写。

也许读者会疑惑，小明自己去送花和代理B帮小明送花，二者看起来并没有本质的区别，引入一个代理对象看起来只是把事情搞复杂了而已。

但是，假设当A在心情好的时候收到花，小明表白成功的几率有60%，而当A在心情差的时候收到花，小明表白的成功率无限趋近于0。

小明跟A刚刚认识两天，还无法辨别A什么时候心情好。如果不合时宜地把花送给A，花被直接扔掉的可能性很大，这束花可是小明吃了7天泡面换来的。

但是A的朋友B却很了解A，所以小明只管把花交给B，B会监听A的心情变化，然后选择A心情好的时候把花转交给A，代码如下：

```
// 首先的有花的实例
var Flower = function(){};
var xiaoming = {
    // 送花的方法，参数为送花目标
    sendFlower: function( target ){
        // 创建一个花的实例
        var flower = new Flower();
        // 送给目标
        target.receiveFlower( flower );
    }
};
// 女神闺蜜
var B = {
    // 小明把花送给了女神闺蜜，由他转送
    receiveFlower: function( flower ){
        // 女神闺蜜看到女神心情好把花送给了女神
       A.listenGoodMood(function(){ // 监听A的好心情
            A.receiveFlower( flower );
        });
    }
};
// 女神
var A = {
    // 女神接收到了花
    receiveFlower: function( flower ){
        console.log( '收到花 ' + flower );
    },
    // 女神心情好了
    listenGoodMood: function( fn ){
        setTimeout(function(){ // 假设10秒之后A的心情变好
            fn();
        }, 10000 );
    }
};
// 小明开始送花
xiaoming.sendFlower( A );
```
### 虚拟代理

虚拟代理把一些开销很大的对象，延迟到真正需要它的时候才去创建。

#### 虚拟代理实现图片预加载

在Web开发中，图片预加载是一种常用的技术，如果直接给某个img标签节点设置src属性， 由于图片过大或者网络不佳，图片的位置往往有段时间会是一片空白。常见的做法是先用一张loading图片占位，然后用异步的方式加载图片，等图片加载好了再把它填充到img节点里，这种场景就很适合使用虚拟代理。

下面我们来实现这个虚拟代理，首先创建一个普通的本体对象，这个对象负责往页面中创建一个img标签，并且提供一个对外的setSrc接口，外界调用这个接口，便可以给该img标签设置src属性：

```
var myImage = (function(){
    var imgNode = document.createElement( 'img' );
    document.body.appendChild( imgNode );
    return {
        setSrc: function( src ){
        i   mgNode.src = src;
        }
    }
})();
myImage.setSrc( 'http:// imgcache.qq.com/music/photo/k/000GGDys0yA0Nk.jpg')

```
引入虚拟代理模式

```
var myImage = (function(){
    var imgNode = document.createElement( 'img' );
    document.body.appendChild( imgNode );
    return {
        setSrc: function( src ){
            imgNode.src = src;
        }
    }
})();
var proxyImage = (function(){
    var img = new Image;
    img.onload = function(){
        myImage.setSrc( this.src );
    }
    return {
        setSrc: function( src ){
         myImage.setSrc( 'file:// /C:/Users/svenzeng/Desktop/loading.gif' );
         img.src = src;
        }
    }
})();
proxyImage.setSrc( 'http://imgcache.qq.com/music/photo/k/000GGDys0yA0Nk.jpg' );
```

现在我们通过proxyImage间接地访问MyImage。proxyImage控制了客户对MyImage的访问，并且在此过程中加入一些额外的操作，比如在真正的图片加载好之前，先把img节点的src设置为一张本地的loading图片。

### 缓存代理

缓存代理可以为一些开销大的运算结果提供暂时的存储，在下次运算时，如果传递进来的参数跟之前一致，则可以直接返回前面存储的运算结果。

#### 缓存代理的例子——计算乘积
 
```
var mult = function(){
    console.log( '开始计算乘积' );
    var a = 1;
    for ( var i = 0, l = arguments.length; i < l; i++ ){
        a = a * arguments[i];
    }
    return a;
};
// 加入缓存代理
var proxyMult = (function(){
    var cache = {};
    return function(){
        var args = Array.prototype.join.call( arguments, ',' );
        if ( args in cache ){
            return cache[ args ];
        }
        return cache[ args ] = mult.apply( this, arguments );
    }
})();
proxyMult( 1, 2, 3, 4 ); // 输出：24
proxyMult( 1, 2, 3, 4 ); // 输出：24

```
当我们第二次调用proxyMult(1,2,3,4)的时候，本体mult函数并没有被计算，proxyMult直接返回了之前缓存好的计算结果。

通过增加缓存代理的方式，mult函数可以继续专注于自身的职责——计算乘积，缓存的功能是由代理对象实现的。

### 其他代理模式
- 防火墙代理：控制网络资源的访问，保护主题不让“坏人”接近。
- 远程代理：为一个对象在不同的地址空间提供局部代表
- 保护代理：用于对象应该有不同访问权限的情况。
- 智能引用代理：取代了简单的指针，它在访问对象时执行一些附加操作，比如计算一个对象被引用的次数。
- 写时复制代理：通常用于复制一个庞大对象的情况。写时复制代理延迟了复制的过程，当对象被真正修改时，才对它进行复制操作。写时复制代理是虚拟代理的一种变体，DLL（操作系统中的动态链接库）是其典型运用场景。
