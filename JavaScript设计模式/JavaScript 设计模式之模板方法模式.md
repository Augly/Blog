### 引

在JavaScript开发中用到继承的场景其实并不是很多，很多时候我们都喜欢用mix-in的方式给对象扩展属性。但这不代表继承在JavaScript里没有用武之地，虽然没有真正的类和继承机制，但我们可以通过原型prototype来变相地实现继承。

模板方法模式是一种只需使用继承就可以实现的非常简单的模式。

模板方法模式由两部分结构组成，第一部分是抽象父类，第二部分是具体的实现子类。通常在抽象父类中封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有方法的执行顺序。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。

假如我们有一些平行的子类，各个子类之间有一些相同的行为，也有一些不同的行为。如果相同和不同的行为都混合在各个子类的实现中，说明这些相同的行为会在各个子类中重复出现。但实际上，相同的行为可以被搬移到另外一个单一的地方，模板方法模式就是为解决这个问题而生的。在模板方法模式中，子类实现中的相同部分被上移到父类中，而将不同的部分留待子类来实现。这也很好地体现了泛化的思想。

### 第一个例子——Coffee or Tea

咖啡与茶是一个经典的例子，经常用来讲解模板方法模式，这个例子的原型来自《Head First设计模式》。这一节我们就用JavaScript来实现这个例子。

#### 先泡一杯咖啡

首先，我们先来泡一杯咖啡，如果没有什么太个性化的需求，泡咖啡的步骤通常如下：

- 把水煮沸
- 用沸水冲泡咖啡
- 把咖啡倒进杯子
- 把咖啡倒进杯子

```
// 先泡一杯咖啡
var Coffee = function(){};
Coffee.prototype.boilWater = function(){
    console.log( '把水煮沸' );
};
Coffee.prototype.brewCoffeeGriends = function(){
    console.log( '用沸水冲泡咖啡' );
};
Coffee.prototype.pourInCup = function(){
    console.log( '把咖啡倒进杯子' );
};
Coffee.prototype.addSugarAndMilk = function(){
    console.log( '加糖和牛奶' );
};
Coffee.prototype.init = function(){
    this.boilWater();
    this.brewCoffeeGriends();
    this.pourInCup();
    this.addSugarAndMilk();
};
var coffee = new Coffee();
coffee.init();
```

#### 泡一壶茶

接下来，开始准备我们的茶，泡茶的步骤跟泡咖啡的步骤相差并不大：

- 把水煮沸
- 用沸水浸泡茶叶
- 把茶水倒进杯子
- 加柠檬

代码实现

```

// 泡一壶茶

var Tea = function(){};
Tea.prototype.boilWater = function(){
    console.log( '把水煮沸' );
};
Tea.prototype.steepTeaBag = function(){
    console.log( '用沸水浸泡茶叶' );
};
Tea.prototype.pourInCup = function(){
    console.log( '把茶水倒进杯子' );
};
Tea.prototype.addLemon = function(){
    console.log( '加柠檬' );
};
Tea.prototype.init = function(){
    this.boilWater();
    this.steepTeaBag();
    this.pourInCup();
    this.addLemon();
};
var tea = new Tea();
tea.init();

```

### 分离出共同点

现在我们分别泡好了一杯咖啡和一壶茶，经过思考和比较，我们发现咖啡和茶的冲泡过程是大同小异的


![](https://user-gold-cdn.xitu.io/2019/12/3/16ecc0f387c3f84a?w=578&h=286&f=png&s=27031)
我们找到泡咖啡和泡茶主要有以下不同点。

- 原料不同。一个是咖啡，一个是茶，但我们可以把它们都抽象
为“饮料”。
- 泡的方式不同。咖啡是冲泡，而茶叶是浸泡，我们可以把它们都抽
象为“泡”。
- 加入的调料不同。一个是糖和牛奶，一个是柠檬，但我们可以把它
们都抽象为“调料”。

经过抽象之后，不管是泡咖啡还是泡茶，我们都能整理为下面四步：

- 把水煮沸
- 用沸水冲泡饮料
- 把饮料倒进杯子
- 加调料

所以，不管是冲泡还是浸泡，我们都能给它一个新的方法名称，比如说brew()。同理，不管是加糖和牛奶，还是加柠檬，我们都可以称之为addCondiments()。

让我们忘记最开始创建的Coffee类和Tea类。现在可以创建一个抽象父类来表示泡一杯饮料的整个过程。不论是Coffee，还是Tea，都被我们用Beverage来表示，代码如下：

```
var Beverage = function(){};

Beverage.prototype.boilWater = function(){
    console.log( '把水煮沸' );
};
Beverage.prototype.brew = function(){}; // 空方法，应该由子类重写
Beverage.prototype.pourInCup = function(){}; // 空方法，应该由子类重写
Beverage.prototype.addCondiments = function(){}; // 空方法，应该由子类重写
Beverage.prototype.init = function(){
    this.boilWater();
    this.brew();
    this.pourInCup();
    this.addCondiments();
};

```

#### 创建Coffee子类和Tea子类

```
var Coffee = function(){};
Coffee.prototype = new Beverage();

// 接下来要重写抽象父类中的一些方法，只有“把水煮沸”这个行为可以直接使用父类Beverage中的boilWater方法，其他方法都需要在Coffee子类中重写

Coffee.prototype.brew = function(){
    console.log( '用沸水冲泡咖啡' );
};
Coffee.prototype.pourInCup = function(){
    console.log( '把咖啡倒进杯子' );
};
Coffee.prototype.addCondiments = function(){
    console.log( '加糖和牛奶' );
};
var Coffee = new Coffee();
Coffee.init();

```
Tea子类

```
var Tea = function(){};
Tea.prototype = new Beverage();
Tea.prototype.brew = function(){
    console.log( '用沸水浸泡茶叶' );
};
Tea.prototype.pourInCup = function(){
    console.log( '把茶倒进杯子' );
};
Tea.prototype.addCondiments = function(){
    console.log( '加柠檬' );
};
var tea = new Tea();
tea.init();
```
那么在上面的例子中，到底谁才是所谓的模板方法呢？答案是Beverage.prototype.init。

Beverage.prototype.init被称为模板方法的原因是，该方法中封装了子类的算法框架，它作为一个算法的模板，指导子类以何种顺序去执行哪些方法。在Beverage.prototype.init方法中，算法内的每一个步骤都清楚地展示在我们眼前。

### 钩子方法

通过模板方法模式，我们在父类中封装了子类的算法框架。这些算法框架在正常状态下是适用于大多数子类的，但如果有一些特别“个性”的子类呢？比如我们在饮料类Beverage中封装了饮料的冲泡顺序：

- 把水煮沸
- 用沸水冲泡饮料
- 把饮料倒进杯子
- 加调料

这4个冲泡饮料的步骤适用于咖啡和茶，在我们的饮料店里，根据这4个步骤制作出来的咖啡和茶，一直顺利地提供给绝大部分客人享用。但有一些客人喝咖啡是不加调料（糖和牛奶）的。既然Beverage作为父类，已经规定好了冲泡饮料的4个步骤，那么有什么办法可以让子类不受这个约束呢？

钩子方法（hook）可以用来解决这个问题，放置钩子是隔离变化的一种常见手段。我们在父类中容易变化的地方放置钩子，钩子可以有一个默认的实现，究竟要不要“挂钩”，这由子类自行决定。钩子方法的返回结果决定了模板方法后面部分的执行步骤，也就是程序接下来的走向，这样一来，程序就拥有了变化的可能。

在这个例子里，我们把挂钩的名字定为customerWantsCondiments，接下来将挂钩放入Beverage类，看看我们如何得到一杯不需要糖和牛奶的咖啡，代码如下：
```
var Beverage = function(){};
Beverage.prototype.boilWater = function(){
    console.log( '把水煮沸' );
};
Beverage.prototype.brew = function(){
    throw new Error( '子类必须重写brew方法' );
};
Beverage.prototype.pourInCup = function(){
    throw new Error( '子类必须重写pourInCup方法' );
};
Beverage.prototype.addCondiments = function(){
    throw new Error( '子类必须重写addCondiments方法' );
};
Beverage.prototype.customerWantsCondiments = function(){
    return true; // 默认需要调料
};
Beverage.prototype.init = function(){
    this.boilWater();
    this.brew();
    this.pourInCup();
    if ( this.customerWantsCondiments() ){ // 如果挂钩返回true，则需要调料
        this.addCondiments();
    }
};
var CoffeeWithHook = function(){};
CoffeeWithHook.prototype = new Beverage();
CoffeeWithHook.prototype.brew = function(){
    console.log( '用沸水冲泡咖啡' );
};
CoffeeWithHook.prototype.pourInCup = function(){
    console.log( '把咖啡倒进杯子' );
};
CoffeeWithHook.prototype.addCondiments = function(){
    console.log( '加糖和牛奶' );
};
CoffeeWithHook.prototype.customerWantsCondiments = function(){
    return window.confirm( '请问需要调料吗？' );
};
var coffeeWithHook = new CoffeeWithHook();
coffeeWithHook.init();
```

### 著名的好莱坞原则

在模板方法模式中我们允许底层组件将自己挂钩到高层组件中，而高层组件会决定什么时候、以何种方式去使用这些底层组件，高层组件对待底层组件的方式，跟演艺公司对待新人演员一样，都是“别调用我们，我们会调用你”。即高层组件调用底层组件。

### 小结

模板方法模式是一种典型的通过封装变化提高系统扩展性的设计模式。在传统的面向对象语言中，一个运用了模板方法模式的程序中，子类的方法种类和执行顺序都是不变的，所以我们把这部分逻辑抽象到父类的模板方法里面。而子类的方法具体怎么实现则是可变的，于是我们把这部分变化的逻辑封装到子类中。通过增加新的子类，我们便能给系统增加新的功能，并不需要改动抽象父类以及其他子类，这也是符合开放封闭原则的。



