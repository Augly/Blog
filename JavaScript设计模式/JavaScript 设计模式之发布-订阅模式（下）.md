### 发布－订阅模式的通用实现

现在我们在上一篇文章中已经看到了如何让售楼处拥有接受订阅和发布事件的功能。假设现在小明又去另一个售楼处买房子，那么这段代码是否必须在另一个售楼处对象上重写一次呢，有没有办法可以让所有对象都拥有发布—订阅功能呢？

答案是肯定的！

如何构建？

首先我们要理解发布订阅设计模式中的基本构造

- 发布
- 订阅集合列表
- 增加订阅方法
- 移除订阅方法

这以上四部即为基本的发布订阅设计模式中的基本构造

首先，我们先创建一个基本对象

```
var event = {
    //订阅集合列表
    clientList: {},
    //增加订阅方法
    listen: function(){},
    //发布
    trigger: function(){},
    //移除订阅
    remove: function(){}
}
```
第一步，完善增加订阅方法

```
var event = {
    //订阅集合列表
    clientList: {},
    //增加订阅方法
    listen: function(methodName,methond){
        //先判断订阅集合列表中是否有这个订阅集合,没有就添加进去集合
        if(!this.clientList[methodName]){
            this.clientList[methodName]=[]
        }
        //订阅的消息添加进已订阅集合
        this.clientList[methodName].push(methond)
    },
    //发布
    trigger: function(){},
    //移除订阅
    remove: function(){}
}
```
第二步，完善发布方法

```
var event = {
    //订阅集合列表
    clientList: {},
    //增加订阅方法
    listen: function(methodName,methond){
        //先判断订阅集合列表中是否有这个订阅集合,没有就添加进去集合
        if(!this.clientList[methodName]){
            this.clientList[methodName]=[]
        }
        //订阅的消息添加进已订阅集合
        this.clientList[methodName].push(methond)
    },
    //发布
    trigger: function(){
        //获取订阅名称
        var methodName=Array.prototype.shift.call(arguments),
            methond=this.clientList[methodName]
        //看订阅集合中是否有这个方法
        if( !methond||methond.length===0 ){
            return false
        }
        //遍历此订阅的方法集合，把参数传进去
        for(let i=0;i<methond.length;i++){
            let fn=methond[i]
            fn.apply.(this,arguments)
        }
    },
    //移除订阅
    remove: function(){}
}

```
最后一步， 移除订阅方法


```
var event = {
    //订阅集合列表
    clientList: {},
    //增加订阅方法
    listen: function(methodName,methond){
        //先判断订阅集合列表中是否有这个订阅集合,没有就添加进去集合
        if(!this.clientList[methodName]){
            this.clientList[methodName]=[]
        }
        //订阅的消息添加进已订阅集合
        this.clientList[methodName].push(methond)
    },
    //发布
    trigger: function(){
        //获取订阅名称
        var methodName=Array.prototype.shift.call(arguments),
            methond=this.clientList[methodName]
        //看订阅集合中是否有这个方法
        if( !methond||methond.length===0 ){
            return false
        }
        //遍历此订阅的方法集合，把参数传进去
        for(let i=0;i<methond.length;i++){
            let fn=methond[i]
            fn.apply.(this,arguments)
        }
    },
    //移除订阅
    remove: function(methodName,methond){
        var fns = this.clientList[ methodName ];
        if ( !fns ){ // 如果key对应的消息没有被人订阅，则直接返回
            return false;
        }
        if ( !methond ){ // 如果没有传入具体的回调函数，表示需要取消key对应消息的所有订阅
            fns && ( fns.length = 0 );
        }else{
            for ( var l = fns.length - 1; l >=0; l-- ){ // 反向遍历订阅的回调函数列表
                var _fn = fns[ l ]; 
                if ( _fn === methond ){
                    fns.splice( l, 1 ); // 删除订阅者的回调函数
                }
            }
        }

    }
}

```
以上就是实现一个基本的发布-订阅的模型

我们也可以写一个安装函数

```

//让某个对象具有发布订阅的功能 
var installEvent = function( obj ){
for ( var i in event ){
    obj[ i ] = event[ i ];
    }
};

```

这样就可以给每个对象安装一个发布订阅装置

### 全局事件的命名冲突

全局的发布—订阅对象里只有一个clinetList来存放消息名和回调函数，大家都通过它来订阅和发布各种消息，久而久之，难免会出现事件名冲突的情况，所以我们还可以给Event对象提供创建命名空间的功能。

在提供最终的代码之前，我们来感受一下怎么使用这两个新增的功能。

```
/************** 先发布后订阅 ********************/
Event.trigger( 'click', 1 );
Event.listen( 'click', function( a ){
    console.log( a ); // 输出：1
});
/************** 使用命名空间 ********************/
Event.create( 'namespace1' ).listen( 'click', function( a ){
    console.log( a ); // 输出：1
});
Event.create( 'namespace1' ).trigger( 'click', 1 );
Event.create( 'namespace2' ).listen( 'click', function( a ){
    console.log( a ); // 输出：2
});
Event.create( 'namespace2' ).trigger( 'click', 2 );

```
具体实现
```
var Event = (function(){
    var global = this,
    Event,
    _default = 'default';
    Event = function(){
        var _listen,
        _trigger,
        _remove,
        _slice = Array.prototype.slice,
        _shift = Array.prototype.shift,
        _unshift = Array.prototype.unshift,
        namespaceCache = {},
        _create,
        find,
        each = function( ary, fn ){
            var ret;
            for ( var i = 0, l = ary.length; i < l; i++ ){
                var n = ary[i];
                ret = fn.call( n, i, n);
            }
            return ret;
        };
        _listen = function( key, fn, cache ){
            if ( !cache[ key ] ){
                cache[ key ] = [];
            }
            cache[key].push( fn );
        };
        _remove = function( key, cache ,fn){
            if ( cache[ key ] ){
                if( fn ){
                    for( var i = cache[ key ].length; i >= 0; i-- ){
                        if( cache[ key ][i] === fn ){
                            cache[ key ].splice( i, 1 );
                        }
                    }
                }else{
                    cache[ key ] = [];
                }
            }
        };
        _trigger = function(){
            var cache = _shift.call(arguments),
            key = _shift.call(arguments),
            args = arguments,
            _self = this,
            ret,
            stack = cache[ key ];
            if ( !stack || !stack.length ){
                return;
            }
            return each( stack, function(){
                return this.apply( _self, args );
            });
        };
        _create = function( namespace ){
            var namespace = namespace || _default;
            var cache = {},
            offlineStack = [], // 离线事件
                ret = {
                listen: function( key, fn, last ){
                    _listen( key, fn, cache );
                    if ( offlineStack === null ){
                        return;
                    }
                    if ( last === 'last' ){
                        offlineStack.length && offlineStack.pop()();
                    }else{
                        each( offlineStack, function(){
                            this();
                        });
                    }
                    offlineStack = null;
                },
                one: function( key, fn, last ){
                    _remove( key, cache );
                    this.listen( key, fn ,last );
                },
                remove: function( key, fn ){
                    _remove( key, cache ,fn);
                },
                trigger: function(){
                    var fn,
                    args,
                    _self = this;
                    _unshift.call( arguments, cache );
                    args = arguments;
                    fn = function(){
                        return _trigger.apply( _self, args );
                    };
                    if ( offlineStack ){
                        return offlineStack.push( fn );
                    }
                    return fn();
                }
            };
            return namespace ?
            ( namespaceCache[ namespace ] ? namespaceCache[ namespace ] :
            namespaceCache[ namespace ] = ret )
            : ret;
        };
        return {
            create: _create,
            one: function( key,fn, last ){
                var event = this.create( );
                event.one( key,fn,last );
            },
            remove: function( key,fn ){
                var event = this.create( );
                event.remove( key,fn );
            },
            listen: function( key, fn, last ){
                var event = this.create( );
                event.listen( key, fn, last );
            },
            trigger: function(){
                var event = this.create( );
                event.trigger.apply( this, arguments );
            }
        };
    }();
    return Event;
})();
```

### 优势与劣势

#### 优势
发布—订阅模式的优点非常明显，一为时间上的解耦，二为对象之间的解耦。它的应用非常广泛，既可以用在异步编程中，也可以帮助我们完成更松耦合的代码编写。发布—订阅模式还可以用来帮助实现一些别的设计模式，比如中介者模式。 从架构上来看，无论是MVC还是MVVM，都少不了发布—订阅模式的参与，而且JavaScript本身也是一门基于事件驱动的语言。

#### 劣势
当然，发布—订阅模式也不是完全没有缺点。创建订阅者本身要消耗一定的时间和内存，而且当你订阅一个消息后，也许此消息最后都未发生，但这个订阅者会始终存在于内存中。另外，发布—订阅模式虽然可以弱化对象之间的联系，但如果过度使用的话，对象和对象之间的必要联系也将被深埋在背后，会导致程序难以跟踪维护和理解。特别是有多个发布者和订阅者嵌套到一起的时候，要跟踪一个bug不是件轻松的事情

    