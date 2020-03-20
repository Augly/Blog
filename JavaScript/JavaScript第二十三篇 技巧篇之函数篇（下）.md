# 函数篇（下）

### 函数绑定
> 函数绑定要创建一个函数，可以在特定的 this 环境中以指定参数调用另一个函数。

为了理解，我们先看一个这样的例子
示例：
```
var handler = {
     message: "Event handled",
     handleClick: function(event){
     alert(this.message);
     }
};

var btn = document.getElementById("my-btn");
EventUtil.addHandler(btn, "click", handler.handleClick); 

```
在这个例子中创建了一个handler对象，当点击Id为"my-btn"的DOM节点以后会触发点击事件，触发的函数为handler.handleClick，然后这个函数本应该显示警告框Event handled，然后实际上却是弹出警告框undefined

因为在调用handler.handleClick的时候this对象最后是指向了 DOM 按钮而非 handler

那么如何解决这个问题？

第一种方案
示例:
```
var handler = {
 message: "Event handled",
 handleClick: function(event){
 alert(this.message);
 }
};
var btn = document.getElementById("my-btn");
EventUtil.addHandler(btn, "click", function(event){
 handler.handleClick(event);
}); 

```
用了一个闭包来直接调用handler.handleClick(event)。并且把event对象传递进去。

第二种方案：创建一个bind()方法，接受一个函数和一个环境，并返回一个在给定环境中调用给定函数的函数，并且将所有参数原封不动传递过去。

示例:
```
function bind(fn, context){
     return function(){
        return fn.apply(context, arguments);
     };
} 

var handler = {
     message: "Event handled",
     handleClick: function(event){
        alert(this.message + ":" + event.type); 
     }
};
var btn = document.getElementById("my-btn");
EventUtil.addHandler(btn, "click", bind(handler.handleClick, handler)); 
```
利用apply把handler.handleClick的作用域绑定在handler上，并且把bind事件上的event参数传递进去

现在直接调用bind()原生函数即可，不需要我们自己实现了，支持原生bind函数的有 IE9+、Firefox 4+和 Chrome。

只要是将某个函数指针以值的形式进行传递，同时该函数必须在特定环境中执行，被绑定函数的效用就突显出来了。它们主要用于事件处理程序以及setTimeout()和setInterval()。然而，被绑定函数与普通函数相比有更多的开销，它们需要更多内存，同时也因为多重函数调用稍微慢一点，所以最好只在必要时使用。

### 函数柯里化

> 与函数绑定紧密相关的主题是函数柯里化（function currying），它用于创建已经设置好了一个或多个参数的函数。函数柯里化的基本方法和函数绑定是一样的：使用一个闭包返回一个函数。两者的区别在于，当函数被调用时，返回的函数还需要设置一些传入的参数。请看以下例子。

```
function add(num1, num2){
    return num1 + num2;
}
function curriedAdd(num2){
    return add(5, num2);
}
alert(add(2, 3)); //5
alert(curriedAdd(3)); //8 

```

这段代码定义了两个函数：add()和curriedAdd()。后者本质上是在任何情况下第一个参数为 5的 add()版本。尽管从技术上来说curriedAdd()并非柯里化的函数，但它很好地展示了其概念。

柯里化函数通常由以下步骤动态创建：调用另一个函数并为它传入要柯里化的函数和必要参数。下面是创建柯里化函数的通用方式。

```
function curry(fn){
    //获取第一个参数后面的参数
     var args = Array.prototype.slice.call(arguments, 1);
     return function(){
        //获取所有参数
         var innerArgs = Array.prototype.slice.call(arguments);
         //最终形成一个数组
         var finalArgs = args.concat(innerArgs);
         return fn.apply(null, finalArgs);
     };
} 

```
curry()函数的主要工作就是将被返回函数的参数进行排序。

curry()的第一个参数是要进行柯里化的函数，其他参数是要传入的值。

为了获取第一个参数之后的所有参数，在arguments对象上调用了slice()方法，并传入参数 1 表示被返回的数组包含从第二个参数开始的所有参数。

然后 args 数组包含了来自外部函数的参数。在内部函数中，创建了 innerArgs 数组用来存放所有传入的参数（又一次用到了slice()）。

有了存放来自外部函数和内部函数的参数数组后，就可以使用 concat()方法将
它们组合为 finalArgs，然后使用apply()将结果传递给该函数。

注意这个函数并没有考虑到执行环境，所以调用 apply()时第一个参数是 null。curry()函数可以按以下方式应用。

```
function add(num1, num2){
 return num1 + num2;
}
var curriedAdd = curry(add, 5);
alert(curriedAdd(3)); //8 

```
但传一个参数时，先获取5，再获取3，最后执行add(5+3)
也可以传2个参数

```
 return num1 + num2;
}
var curriedAdd = curry(add, 5, 12);
alert(curriedAdd()); //17 

```
这样的话先直接就获取了5，12，然后再获取的时候没有参数，所以直接就是add(5+12)

JavaScript 中的柯里化函数和绑定函数提供了强大的动态函数创建功能。使用bind()还是 curry()要根据是否需要object对象响应来决定。它们都能用于创建复杂的算法和功能，当然两者都不应滥用，因为每个函数都会带来额外的开销。



