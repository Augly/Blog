### 避免全局查找
    
可能优化脚本性能最重要的就是注意全局查找。使用全局变量和函数肯定要比局部的开销更大，因为要涉及作用域链上的查找。将在一个函数中会用到多次的全局对象存储为局部变量总是没错的。 特别是使用链式操作的时候，如果涉及到全局变量，应把全局变量赋予给局部变量
    
### 避免 with 语句 

在性能非常重要的地方必须避免使用 with 语句。和函数类似，with 语句会创建自己的作用域，因此会增加其中执行的代码的作用域链的长度。由于额外的作用域链查找，在 with 语句中执行的代码肯定会比外面执行的代码要慢。

###  避免不必要的属性查找

一般来讲，只要能减少算法的复杂度，就要尽可能减少。尽可能多地使用局部变量将属性查找替换为值查找。进一步讲，如果即可以用数字化的数组位置进行访问，也可以使用命名属性（诸如 NodeList对象），那么使用数字位置。

### 优化循环

循环是编程中最常见的结构，在JavaScript程序中同样随处可见。优化循环是性能优化过程中很重要的一个部分，由于它们会反复运行同一段代码，从而自动地增加执行时间。在其他语言中对于循环优化有大量研究，这些技术也可以应用于 JavaScript。一个循环的基本优化步骤如下所示。

- 减值迭代：大多数循环使用一个从0开始、增加到某个特定值的迭代器。在很多情况下从最大值开始，在循环中不断减值的迭代器更加高效。 
- 简化终止条件：由于每次循环过程都会计算终止条件，所以必须保证它尽可能快。也就是说避免属性查找或其他 O(n)的操作。
- 简化循环体：循环体是执行最多的，所以要确保其被最大限度地优化。确保没有某些可以被很容易移出循环的密集计算。
- 使用后测试循环：最常用 for 循环和 while 循环都是前测试循环。而如 do-while 这种后测试循环，可以避免最初终止条件的计算，因此运行更快。

#### Duff装置

Duff 装置的基本概念是通过计算迭代的次数是否为8的倍数将一个循环展开为一系列语句。

示例:
```
//假设 values.length > 0
var iterations = Math.ceil(values.length / 8);
var startAt = values.length % 8;
var i = 0;
do {
     switch(startAt){
         case 0: process(values[i++]);
         case 7: process(values[i++]);
         case 6: process(values[i++]);
         case 5: process(values[i++]);
         case 4: process(values[i++]);
         case 3: process(values[i++]);
         case 2: process(values[i++]);
         case 1: process(values[i++]);
     }
     startAt = 0;
} while (--iterations > 0); 
```

更快的实现方法
```
//credit: Speed Up Your Site (New Riders, 2003)
var iterations = Math.floor(values.length / 8);
var leftover = values.length % 8;
var i = 0;
if (leftover > 0){
     do {
        process(values[i++]);
     } while (--leftover > 0);
}
do {
     process(values[i++]); 
     process(values[i++]);
     process(values[i++]);
     process(values[i++]);
     process(values[i++]);
     process(values[i++]);
     process(values[i++]);
     process(values[i++]);
} while (--iterations > 0); 
```

### 性能的其他注意事项 
- 原生方法较快：只要有可能，使用原生方法而不是自己用 JavaScript 重写一个。原生方法是用诸如 C/C++之类的编译型语言写出来的，所以要比 JavaScript 的快很多很多。JavaScript 中最容易被忘记的就是可以在 Math 对象中找到的复杂的数学运算；这些方法要比任何用 JavaScript 写的同样方法如正弦、余弦快的多。 
- Switch 语句较快：如果有一系列复杂的 if-else 语句，可以转换成单个 switch 语句则可以得到更快的代码。还可以通过将case语句按照最可能的到最不可能的顺序进行组织，来进一步优化 switch 语句。 
- 位运算符较快：当进行数学运算的时候，位运算操作要比任何布尔运算或者算数运算快。选择性地用位运算替换算数运算可以极大提升复杂计算的性能。诸如取模，逻辑与和逻辑或都可以考虑用位运算来替换。

### 多个变量声明合并成一个语句
示例:
```
//4 个语句——很浪费
var count = 5;
var color = "blue";
var values = [1,2,3];
var now = new Date(); 
```
优化后:
```
//一个语句
var count = 5,
 color = "blue",
 values = [1,2,3],
 now = new Date(); 
```

###  使用数组和对象字面量

尽可能使用数组和对象字面量

### 尽可能少使用现场更新

### 使用 innerHTML 

### 使用事件代理 