### 什么是迭代器模式？

迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素。

#### jQuery中的迭代器

迭代器模式无非就是循环访问聚合对象中的各个元素。比如jQuery中的$.each函数，其中回调函数中的参数i为当前索引，n为当前元素，代码如下：

```
$.each( [1, 2, 3], function( i, n ){
    console.log( '当前下标为： '+ i );
    console.log( '当前值为:' + n );
});
```

#### 实现自己的迭代器

现在我们来自己实现一个each函数，each函数接受2个参数，第一个为被循环的数组，第二个为循环中的每一步后将被触发的回调函数：

```
var each = function( ary, callback ){
    for ( var i = 0, l = ary.length; i < l; i++ ){
    callback.call( ary[i], i, ary[ i ] ); // 把下标和元素当作参数传给callback函数
    }
};
each( [ 1, 2, 3 ], function( i, n ){
    alert ( [ i, n ] );
});
```

#### 内部迭代器和外部迭代器

迭代器可以分为内部迭代器和外部迭代器，它们有各自的适用场景。这一节我们将分别讨论这两种迭代器。

1. 内部迭代器

内部迭代器的内部已经定义好了迭代规则，它完全接手整个迭代过程，外部只需要一次初始调用。

如each函数就是一个内部迭代器

2. 外部迭代器

外部迭代器必须显式地请求迭代下一个元素。外部迭代器增加了一些调用的复杂度，但相对也增强了迭代器的灵活性，我们可以手工控制迭代的过程或者顺序。

如下:
```
var Iterator = function( obj ){
    //初始化索引
    var current = 0;
    //下一个
    var next = function(){
        current += 1;
    };
    //如果当前的索引大于或者等于参数的个数，则已经是最后一个
    var isDone = function(){
        return current >= obj.length;
    };
    //获取当前迭代对象
    var getCurrItem = function(){
        return obj[ current ];
    };
    //返回
    return {
        next: next,
        isDone: isDone,
        getCurrItem: getCurrItem
    }
};


```

外部迭代器虽然调用方式相对复杂，但它的适用面更广，也能满足更多变的需求。内部迭代器和外部迭代器在实际生产中没有优劣之分，究竟使用哪个要根据需求场景而定。


#### 迭代类数组对象和字面量对象

迭代器模式不仅可以迭代数组，还可以迭代一些类数组的对象。在JavaScript中，for in语句可以用来迭代普通字面量对象的属性。

#### 倒序迭代器

只需要把迭代器进行递减操作

如下:
```
var reverseEach = function( ary, callback ){
    for ( var l = ary.length - 1; l >= 0; l-- ){
        callback( l, ary[ l ] );
    }
};
reverseEach( [ 0, 1, 2 ], function( i, n ){
    console.log( n ); // 分别输出：2, 1 ,0
});

```
#### 中止迭代器

迭代器可以像普通for循环中的break一样，提供一种跳出循环的方法。

示例如下:
```
var each = function( ary, callback ){
    for ( var i = 0, l = ary.length; i < l; i++ ){
        if ( callback( i, ary[ i ] ) === false ){ //                            callback的执行结果返回false，提前终止迭代
            break;
        }
    }
};
each( [ 1, 2, 3, 4, 5 ], function( i, n ){
    if ( n > 3 ){ // n大于3的时候终止循环
        return false;
    }
    console.log( n ); // 分别输出：1, 2, 3
});
```


迭代器模式是一种相对简单的模式，目前的绝大部分语言都内置了迭代器。