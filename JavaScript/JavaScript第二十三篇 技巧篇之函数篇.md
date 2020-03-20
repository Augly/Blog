# 函数篇(上)

#### 安全的类型检测

JavaScript 内置的类型检测机制并非完全可靠。事实上，发生错误否定及错误肯定的情况也不在少数。比如说 typeof操作符吧，由于它有一些无法预知的行为，经常会导致检测数据类型时得到不靠谱的结果。Safari（直至第4版）在对正则表达式应用typeof操作符时会返回"function"，因此很难确定某个值到底是不是函数

再比如，instanceof 操作符在存在多个全局作用域（像一个页面包含多个 frame）的情况下，也是问题多多。

如何解决上面的这些问题呢？

> 大家知道，在任何值上调用 Object 原生的 toString()方法，都会返回一个[object NativeConstructorName]格式的字符串。每个类在内部都有一个[[Class]]属
> 性，这个属性中就指定了上述字符串中的构造函数名。

示例：
```
alert(Object.prototype.toString.call(value)); 
//判断是不是数组
function isArray(value){
 return Object.prototype.toString.call(value) == "[object Array]";
} 
//判断是不是函数
function isFunction(value){
 return Object.prototype.toString.call(value) == "[object Function]";
} 
//判断是不是正则
function isRegExp(value){
 return Object.prototype.toString.call(value) == "[object RegExp]";
} 
```

#### 作用域安全的构造函数

什么是构造函数？
构造函数其实就是一种用new 操作符调用的函数，并且该函数的作用域会指向新创建的的对象实例

示例：
```
//新建一个Person函数
function Person(name, age, job){
     this.name = name;
     this.age = age;
     this.job = job;
} 
//创建一个person构造函数，这时Person的this就指向于person
var person = new Person("Nicholas", 29, "Software Engineer");

```

这是一个正常的构造函数对吧，但是如果你忘记写了new操作符呢？我们一起来看看

示例：
```
//新建一个Person函数
function Person(name, age, job){
     this.name = name;
     this.age = age;
     this.job = job;
} 
//创建一个person构造函数，但是忘记了写new操作符，这时候this指向谁呢？
var person =  Person("Nicholas", 29, "Software Engineer");

```
答案是Person函数的上一层作用域，在此处也就是windows

那么这个问题如何让解决呢？

解决方案就是创建一个作用域安全的构造函数。

示例:
```
//创建一个Person函数
function Person(name, age, job){
    // 判断this是不是Persoon的实例
     if (this instanceof Person){
         this.name = name;
         this.age = age;
         this.job = job;
     } else {
        // 如果不是就new Person
        return new Person(name, age, job);
     }
} 

//这个函数会在没有写new操作符的情况下自动创建一个带有new 操作符的构造函数

```

最后的结果是，调用 Person 构造函数时无论是否使用 new 操作符，都会返回一个 Person 的新实例，这就避免了在全局对象上意外设置属性。


#### 惰性载入函数
惰性载入表示函数执行的分支仅会发生一次。有两种实现惰性载入的方式，第一种就是在函数被调用时再处理函数。在第一次调用的过程中，该函数会被覆盖为另外一个按合适方式执行的函数，这样任何对原函数的调用都不用再经过执行的分支了。

示例:
```
function createXHR(){
     if (typeof XMLHttpRequest != "undefined"){
         createXHR = function(){ 
            return new XMLHttpRequest();
        };
     } else if (typeof ActiveXObject != "undefined"){
         createXHR = function(){
             if (typeof arguments.callee.activeXString != "string"){
                 var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",
                 "MSXML2.XMLHttp"],
                 i, len;
                 for (i=0,len=versions.length; i < len; i++){
                     try {
                         new ActiveXObject(versions[i]);
                         arguments.callee.activeXString = versions[i];
                         break;
                     } catch (ex){
                     //skip
                     }
                 }
             }
             return new ActiveXObject(arguments.callee.activeXString);
         };
     } else {
         createXHR = function(){
            throw new Error("No XHR object available.");
         };
     }
     return createXHR();
} 
```

第二种实现惰性载入的方式是在声明函数时就指定适当的函数。这样，第一次调用函数时就不会损失性能了，而在代码首次加载时会损失一点性能。

示例：
```
var createXHR = (function(){
     if (typeof XMLHttpRequest != "undefined"){
         return function(){
            return new XMLHttpRequest();
         };
     } else if (typeof ActiveXObject != "undefined"){
         return function(){
             if (typeof arguments.callee.activeXString != "string"){
                 var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",
                 "MSXML2.XMLHttp"],
                 i, len;
                 for (i=0,len=versions.length; i < len; i++){
                     try {
                         new ActiveXObject(versions[i]);
                         arguments.callee.activeXString = versions[i];
                        break;
                     } catch (ex){
                         //skip
                     }
                 }
                     
             }
             return new ActiveXObject(arguments.callee.activeXString);
         };
     } else {
         return function(){
             throw new Error("No XHR object available.");
         };
     }
})(); 
```
这个例子中使用的技巧是创建一个匿名、自执行的函数，用以确定应该使用哪一个函数实现。实际的逻辑都一样。不一样的地方就是第一行代码（使用 var 定义函数）、新增了自执行的匿名函数，另外每个分支都返回正确的函数定义，以便立即将其赋值给 createXHR()。