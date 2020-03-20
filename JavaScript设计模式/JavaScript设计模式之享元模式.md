### 什么是享元模式?

享元（flyweight）模式是一种用于性能优化的模式，“fly”在这里是苍蝇的意思，意为蝇量级。享元模式的核心是运用共享技术来有效支持大量细粒度的对象。

如果系统中因为创建了大量类似的对象而导致内存占用过高，享元模式就非常有用了。在JavaScript中，浏览器特别是移动端的浏览器分配的内存并不算多，如何节省内存就成了一件非常有意义的事情。

享元模式的概念初听起来并不太好理解，所以在深入讲解之前，我们先看一个例子。

### 初识享元模式

假设有个内衣工厂，目前的产品有50种男式内衣和50种女士内衣，为了推销产品，工厂决定生产一些塑料模特来穿上他们的内衣拍成广告照片。 正常情况下需要50个男模特和50个女模特，然后让他们每人分别穿上一件内衣来拍照。不使用享元模式的情况下，在程序里也许会这样写：

```
var Model = function( sex, underwear){
    this.sex = sex;
    this.underwear= underwear;
};
Model.prototype.takePhoto = function(){
    console.log( 'sex= ' + this.sex + ' underwear=' + this.underwear);
};
for ( var i = 1; i <= 50; i++ ){
    var maleModel = new Model( 'male', 'underwear' + i );
    maleModel.takePhoto();
};
for ( var j = 1; j <= 50; j++ ){
    var femaleModel= new Model( 'female', 'underwear' + j );
    femaleModel.takePhoto();
};
```

要得到一张照片，每次都需要传入sex和underwear参数，如上所述，现在一共有50种男内衣和50种女内衣，所以一共会产生100个对象。如果将来生产了10000种内衣，那这个程序可能会因为存在如此多的对已经提前崩溃。

下面我们来考虑一下如何优化这个场景。虽然有100种内衣，但很显然并不需要50个男模特和50个女模特。其实男模特和女模特各自有一个就足够了，他们可以分别穿上不同的内衣来拍照。

现在来改写一下代码，既然只需要区别男女模特，那我们先把underwear参数从构造函数中移除，构造函数只接收sex参数：

```
var Model = function( sex ){
    this.sex = sex;
};
Model.prototype.takePhoto = function(){
    console.log( 'sex= ' + this.sex + ' underwear=' + this.underwear);
};
```
分别创建一个男模特对象和一个女模特对象：

```
var maleModel = new Model( 'male' ),
femaleModel = new Model( 'female' );
```
给男模特依次穿上所有的男装，并进行拍照：

```
for ( var i = 1; i <= 50; i++ ){
    maleModel.underwear = 'underwear' + i;
    maleModel.takePhoto();
};
```
同样，给女模特依次穿上所有的女装，并进行拍照：

```
for ( var j = 1; j <= 50; j++ ){
    femaleModel.underwear = 'underwear' + j;
    femaleModel.takePhoto();
};
```
可以看到，改进之后的代码，只需要两个对象便完成了同样的功能。

### 内部状态与外部状态

上面那个例子便是享元模式的雏形，享元模式要求将对象的属性划分为内部状态与外部状态（状态在这里通常指属性）。享元模式的目标是尽量减少共享对象的数量，关于如何划分内部状态和外部状态，下面的几条经验提供了一些指引。

- 内部状态存储于对象内部。
- 内部状态可以被一些对象共享。
- 内部状态独立于具体的场景，通常不会改变。
- 外部状态取决于具体的场景，并根据场景而变化，外部状态不能被共享。

这样一来，我们便可以把所有内部状态相同的对象都指定为同一个共享的对象。而外部状态可以从对象身上剥离出来，并储存在外部。

剥离了外部状态的对象成为共享对象，外部状态在必要时被传入共享对象来组装成一个完整的对象。虽然组装外部状态成为一个完整对象的过程需要花费一定的时间，但却可以大大减少系统中的对象数量，相比之下，这点时间或许是微不足道的。因此，享元模式是一种用时间换空间的优化模式。

在上面的例子中，性别是内部状态，内衣是外部状态，通过区分这两种状态，大大减少了系统中的对象数量。通常来讲，内部状态有多少种组合，系统中便最多存在多少个对象，因为性别通常只有男女两种，所以该内衣厂商最多只需要2个对象。

使用享元模式的关键是如何区别内部状态和外部状态。可以被对象共享的属性通常被划分为内部状态，如同不管什么样式的衣服，都可以按照性别不同，穿在同一个男模特或者女模特身上，模特的性别就可以作为内部状态储存在共享对象的内部。而外部状态取决于具体的场景，并根据场景而变化，就像例子中每件衣服都是不同的，它们不能被一些对象共享，因此只能被划分为外部状态。

### 享元模式的适用性

享元模式是一种很好的性能优化方案，但它也会带来一些复杂性的问题，从前面两组代码的比较可以看到，使用了享元模式之后，我们需要分别多维护一个factory对象和一个manager对象，在大部分不必要使用享元模式的环境下，这些开销是可以避免的。

享元模式带来的好处很大程度上取决于如何使用以及何时使用，一般来说，以下情况发生时便可以使用享元模式。

- 一个程序中使用了大量的相似对象。
- 由于使用了大量对象，造成很大的内存开销
- 对象的大多数状态都可以变为外部状态
- 剥离出对象的外部状态之后，可以用相对较少的共享对象取代大量对象。

### 再谈内部状态和外部状态

我们知道，实现享元模式的关键是把内部状态和外部状态分离开来。有多少种内部状态的组合，系统中便最多存在多少个共享对象，而外部状态储存在共享对象的外部，在必要时被传入共享对象来组装成一个完整的对象。现在来考虑两种极端的情况，即对象没有外部状态和没有内部状态的时候。

### 对象池

对象池维护一个装载空闲对象的池子，如果需要对象的时候，不是直接new，而是转从对象池里获取。如果对象池里没有空闲对象，
则创建一个新的对象，当获取出的对象完成它的职责之后， 再进入池子等待被下次获取。

对象池的原理很好理解，比如我们组人手一本《JavaScript权威指南》，从节约的角度来讲，这并不是很划算，因为大部分时间这些书都被闲置在各自的书架上，所以我们一开始就只买一本，或者一起建立一个小型图书馆（对象池），需要看书的时候就从图书馆里借，看完了之后再把书还回图书馆。如果同时有三个人要看这本书，而现在图书馆里只有两本，那我们再马上去书店买一本放入图书馆。

对象池技术的应用非常广泛，HTTP连接池和数据库连接池都是其代表应用。在Web前端开发中，对象池使用最多的场景大概就是跟DOM有关的操作。很多空间和时间都消耗在了DOM节点上，如何避免频繁地创建和删除DOM节点就成了一个有意义的话题。

#### 对象池实现

假设我们在开发一个地图应用， 地图上经常会出现一些标志地名的小气泡，我们叫它toolTip。如图所示。


![](https://user-gold-cdn.xitu.io/2019/12/5/16ed6731de96afb8?w=527&h=302&f=png&s=79550)

在搜索我家附近地图的时候，页面里出现了2个小气泡。当我再搜索附近的兰州拉面馆时，页面中出现了6个小气泡。按照对象池的思想，在第二次搜索开始之前，并不会把第一次创建的2个小气泡删除掉，而是把它们放进对象池。这样在第二次的搜索结果页面里，我们只需要再创建4个小气泡而不是6个


![](https://user-gold-cdn.xitu.io/2019/12/5/16ed67554ed93dcf?w=566&h=299&f=png&s=80106)

先定义一个获取小气泡节点的工厂，作为对象池的数组成为私有属性被包含在工厂闭包里，这个工厂有两个暴露对外的方法，create表示获取一个div节点，recover表示回收一个div节点：

```
var toolTipFactory = (function(){
    var toolTipPool = []; // toolTip对象池
    return {
        create: function(){
            if ( toolTipPool.length === 0 ){ // 如果对象池为空
                var div = document.createElement( 'div' ); // 创建一个dom
                document.body.appendChild( div );
                return div;
            }else{ // 如果对象池里不为空
                return toolTipPool.shift(); // 则从对象池中取出一个dom
            }
        },
        recover: function( tooltipDom ){
        return toolTipPool.push( tooltipDom ); // 对象池回收dom
        }
    }
})();
```

现在把时钟拨回进行第一次搜索的时刻，目前需要创建2个小气泡节点，为了方便回收，用一个数组ary来记录它们：

```
var ary = [];
for ( var i = 0, str; str = [ 'A', 'B' ][ i++ ]; ){
    var toolTip = toolTipFactory.create();
    toolTip.innerHTML = str;
    ary.push( toolTip );
};
```

如果你愿意稍稍测试一下，可以看到页面中出现了innerHTML分别为A和B的两个div节点。

接下来假设地图需要开始重新绘制，在此之前要把这两个节点回收进对象池：

```
for ( var i = 0, toolTip; toolTip = ary[ i++ ]; ){
    toolTipFactory.recover( toolTip );
};
```
再创建6个小气泡：

```
for ( var i = 0, str; str = [ 'A', 'B', 'C', 'D', 'E', 'F' ][ i++ ]; ){
    var toolTip = toolTipFactory.create();
    toolTip.innerHTML = str;
};

```


现在再测试一番，页面中出现了内容分别为A、B、C、D、E、F的6个节点，上一次创建好的节点被共享给了下一次操作。对象池跟享元模式的思想有点相似，虽然innerHTML的值A、B、C、D等也可以看成节点的外部状态，但在这里我们并没有主动分离内部状态和外部状态的过程。

#### 通用对象池实现

我们还可以在对象池工厂里，把创建对象的具体过程封装起来，实现一个通用的对象池：

```
var objectPoolFactory = function( createObjFn ){
    var objectPool = [];
    return {
        create: function(){
            var obj = objectPool.length === 0 ?
            createObjFn.apply( this, arguments ) : objectPool.shift();
            return obj;
        },
        recover: function( obj ){
        objectPool.push( obj );
        }
    }
};

```

现在利用objectPoolFactory来创建一个装载一些iframe的对象池：

```
var iframeFactory = objectPoolFactory( function(){
    var iframe = document.createElement( 'iframe' );
    document.body.appendChild( iframe );
    iframe.onload = function(){
        iframe.onload = null; // 防止iframe重复加载的bug
        iframeFactory.recover( iframe ); // iframe加载完成之后回收节点
    }
    return iframe;
});
var iframe1 = iframeFactory.create();
iframe1.src = 'http:// baidu.com';
var iframe2 = iframeFactory.create();
iframe2.src = 'http:// QQ.com';
setTimeout(function(){
    var iframe3 = iframeFactory.create();
    iframe3.src = 'http:// 163.com';
}, 3000 );

```

对象池是另外一种性能优化方案，它跟享元模式有一些相似之处，但没有分离内部状态和外部状态这个过程。本章用享元模式完成了一个文件上传的程序，其实也可以用对象池+事件委托来代替实现。

享元模式是为解决性能问题而生的模式，这跟大部分模式的诞生原因都不一样。在一个存在大量相似对象的系统中，享元模式可以很好地解决大量对象带来的性能问题。