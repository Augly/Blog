任何对象都可以被在同一环境中运行的代码修改。

开发人员很可能会意外地修改别人的代码，甚至更糟糕地，用不兼容的功能重写原生对象。

ECMAScript 5 致力于解决这个问题，可以让开发人员定义防篡改对象（tamper-proof object）。

### 不可扩展对象（第一级别）

所有对象都是可以扩展的。也就是说，任何时候都可以向对象中添加属性和方法。

示例：
```
// 定义一个对象
var person = { name: "Nicholas" };
// 给对象添加一个属性
person.age = 29;

```
这时候person就多了一个属性age，那如何让person不能添加属性呢？

这时候可以调用 **Object.preventExtensions()** 这个方法可以让person不能再添加属性

示例:
```
var person = { name: "Nicholas" };
Object.preventExtensions(person);
person.age = 29;
alert(person.age); //undefined 
```
在调用了 **Object.preventExtensions()** 方法后，就不能给 person 对象添加新属性和方法了。但是依然可以给对象修改和删除原有属性

**Object.istExtensible(obj)** 可以用来判断对象obj是不是可以扩展，可以扩展就返回true，否则返回false

### 密封对象(第二级别)

密封对象不可扩展，而且已有成员的[[Configurable]]特性将被设置为 false。

这就意味着不能删除属性和方法，因为不能使用Object.defineProperty()把数据属性修改为访问器属性，或者相反。属性值是可以修改的。

要密封对象，可以使用 **Object.seal()** 方法。

示例:
```
var person = { name: "Nicholas" };
Object.seal(person);
person.age = 29;
alert(person.age); //undefined
delete person.name;
alert(person.name); //"Nicholas" 

```
在这个例子中，添加 age属性的行为被忽略了。而尝试删除name属性的操作也被忽略了，因此这个属性没有受任何影响。

使用 **Object.isSealed()** 方法可以确定对象是否被密封了。因为被密封的对象不可扩展，所以用 **Object.isExtensible()** 检测密封的对象也会返回 false。

示例:
```
var person = { name: "Nicholas" };
alert(Object.isExtensible(person))  //true
alert(Object.isSealed(person)) //false

Object.seal(person)
alert(Object.isExtensible(person))  //false
alert(Object.isSealed(person)) //true
```

#### 冻结对象(第三级别最高级别)

最严格的防篡改级别是冻结对象（frozen object）。冻结的对象既不可扩展，又是密封的，而且对象数据属性的[[Writable]]特性会被设置为 false。如果定义[[Set]]函数，访问器属性仍然是可写的。ECMAScript 5 定义的 Object.freeze()方法可以用来冻结对象。

示例:
```
var person = { name: "Nicholas" };
Object.freeze(person);
person.age = 29;
alert(person.age); //undefined
delete person.name;
alert(person.name); //"Nicholas"
person.name = "Greg";
alert(person.name); //"Nicholas" 

```

冻结对象不可添加属性和方法，默认也不可修改属性和方法

通常用于核心代码，以防被人修改
