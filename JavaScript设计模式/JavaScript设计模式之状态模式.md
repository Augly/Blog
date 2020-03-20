### 什么是状态模式？

状态模式是一种非同寻常的优秀模式，它也许是解决某些需求场景的最好方法。虽然状态模式并不是一种简单到一目了然的模式（它往往还会带来代码量的增加），但你一旦明白了状态模式的精髓，以后一定会感谢它带给你的无与伦比的好处。

状态模式的关键是区分事物内部的状态，事物内部状态的改变往往会带来事物的行为改变。

### 初识状态模式

我们来想象这样一个场景：有一个电灯，电灯上面只有一个开关。当电灯开着的时候，此时按下开关，电灯会切换到关闭状态；再按一次开关，电灯又将被打开。同一个开关按钮，在不同的状态下，表现出来的行为是不一样的。



![](https://user-gold-cdn.xitu.io/2019/12/9/16eeab5c2dee9deb?w=626&h=360&f=png&s=92589)
现在用代码来描述这个场景，首先定义一个Light类，可以预见，电灯对象light将从Light类创建而出， light对象将拥有两个属性，我们用state来记录电灯当前的状态，用button表示具体的开关按钮。下面
来编写这个电灯程序的例子。

#### 第一个例子：电灯程序
首先给出不用状态模式的电灯程序实现：
```
var Light = function(){
    this.state = 'off'; // 给电灯设置初始状态off
    this.button = null; // 电灯开关按钮
};
```
接下来定义Light.prototype.init方法，该方法负责在页面中创建一个真实的button节点，假设这个button就是电灯的开关按钮，
当button的onclick事件被触发时，就是电灯开关被按下的时候，代码如下：
```
Light.prototype.init = function(){
    var button = document.createElement( 'button' ),
    self = this;
    button.innerHTML = '开关';
    this.button = document.body.appendChild( button );
    this.button.onclick = function(){
        self.buttonWasPressed();
    }
};
```
当开关被按下时，程序会调用self.buttonWasPressed方法， 开关按下之后的所有行为，都将被封装在这个方法里，代码如下：
```
Light.prototype.buttonWasPressed = function(){
    if ( this.state === 'off' ){
        console.log( '开灯' );
        this.state = 'on';
    }else if ( this.state === 'on' ){
        console.log( '关灯' );
        this.state = 'off';
    }
};
var light = new Light();
light.init();
```
OK，现在可以看到，我们已经编写了一个强壮的状态机，这个状态机的逻辑既简单又缜密，看起来这段代码设计得无懈可击，这个程序没有任何bug。实际上这种代码我们已经编写过无数遍，比如要交替切换一个button的class，跟此例一样，往往先用一个变量state来记录按钮的当前状态，在事件发生时，再根据这个状态来决定下一步的行为。

令人遗憾的是，这个世界上的电灯并非只有一种。许多酒店里有另外一种电灯，这种电灯也只有一个开关，但它的表现是：第一次按下打开弱光，第二次按下打开强光，第三次才是关闭电灯。现在必须改造上面的代码来完成这种新型电灯的制造：
```
Light.prototype.buttonWasPressed = function(){
    if ( this.state === 'off' ){
        console.log( '弱光' );
        this.state = 'weakLight';
    }else if ( this.state === 'weakLight' ){
        console.log( '强光' );
        this.state = 'strongLight';
    }else if ( this.state === 'strongLight' ){
        console.log( '关灯' );
        this.state = 'off';
    }
};

```
现在这个反例先告一段落，我们来考虑一下上述程序的缺点。

- 很明显buttonWasPressed方法是违反开放-封闭原则的，每次新增或者修改light的状态，都需要改动buttonWasPressed方法中的
代码，这使得buttonWasPressed成为了一个非常不稳定的方法。

- 所有跟状态有关的行为，都被封装在buttonWasPressed方法里，如果以后这个电灯又增加了强强光、超强光和终极强光，那我们将
无法预计这个方法将膨胀到什么地步。当然为了简化示例，此处在状态发生改变的时候，只是简单地打印一条log和改变button的
innerHTML。在实际开发中，要处理的事情可能比这多得多，也就是说，buttonWasPressed方法要比现在庞大得多。

- 状态的切换非常不明显，仅仅表现为对state变量赋值，比如this.state='weakLight'。在实际开发中，这样的操作很容易被程序员不小心漏掉。我们也没有办法一目了然地明白电灯一共有多少种状态，除非耐心地读完buttonWasPressed方法里的所有代
码。当状态的种类多起来的时候，某一次切换的过程就好像被埋藏在一个巨大方法的某个阴暗角落里。

- 状态之间的切换关系，不过是往buttonWasPressed方法里堆砌if、else语句，增加或者修改一个状态可能需要改变若干个操作，这使buttonWasPressed更加难以阅读和维护。

#### 状态模式改进电灯程序

现在我们学习使用状态模式改进电灯的程序。有意思的是，通常我们谈到封装，一般都会优先封装对象的行为，而不是对象的状态。但在状态模式中刚好相反，状态模式的关键是把事物的每种状态都封装成单独的类，跟此种状态有关的行为都被封装在这个类的内部，所以button被按下的的时候，只需要在上下文中，把这个请求委托给当前的状态对象即可，该状态对象会负责渲染它自身的行为，如图所示。

![](https://user-gold-cdn.xitu.io/2019/12/9/16eeaba978c6cf18?w=629&h=730&f=png&s=55617)

同时我们还可以把状态的切换规则事先分布在状态类中， 这样就有效地消除了原本存在的大量条件分支语句，

![](https://user-gold-cdn.xitu.io/2019/12/9/16eeabb7c6d85d0b?w=631&h=267&f=png&s=45706)

下面进入状态模式的代码编写阶段，首先将定义3个状态类，分别是OffLightState、WeakLightState、StrongLightState。这3个类
都有一个原型方法buttonWasPressed，代表在各自状态下，按钮被按下时将发生的行为，代码如下：
```
// OffLightState：
var OffLightState = function( light ){
    this.light = light;
};
OffLightState.prototype.buttonWasPressed = function(){
    console.log( '弱光' ); // offLightState对应的行为
    this.light.setState( this.light.weakLightState ); // 切换状态到weakLightState
};
// WeakLightState：
var WeakLightState = function( light ){
    this.light = light;
};
WeakLightState.prototype.buttonWasPressed = function(){
    console.log( '强光' ); // weakLightState对应的行为
    this.light.setState( this.light.strongLightState ); // 切换状态到strongLightState
};
// StrongLightState：
var StrongLightState = function( light ){
    this.light = light;
};
StrongLightState.prototype.buttonWasPressed = function(){
    console.log( '关灯' ); // strongLightState对应的行为
    this.light.setState( this.light.offLightState ); // 切换状态到offLightState
};

```
接下来改写Light类，现在不再使用一个字符串来记录当前的状态，而是使用更加立体化的状态对象。我们在Light类的构造函数里为每个状态类都创建一个状态对象，这样一来我们可以很明显地看到电灯一共有多少种状态，代码如下：

```
var Light = function(){
    this.offLightState = new OffLightState( this );
    this.weakLightState = new WeakLightState( this );
    this.strongLightState = new StrongLightState( this );
    this.button = null;
};
```
在button按钮被按下的事件里，Context也不再直接进行任何实质性的操作，而是通过self.currState.buttonWasPressed()将请求委托给当前持有的状态对象去执行，代码如下：

```
Light.prototype.init = function(){
    var button = document.createElement( 'button' ),
    self = this;
    this.button = document.body.appendChild( button );
    this.button.innerHTML = '开关';
    this.currState = this.offLightState; // 设置当前状态
    this.button.onclick = function(){
        self.currState.buttonWasPressed();
    }
};

```
最后还要提供一个Light.prototype.setState方法，状态对象可以通过这个方法来切换light对象的状态。前面已经说过，状态的切换规律事先被完好定义在各个状态类中。在Context中再也找不到任何一个跟状态切换相关的条件分支语句：
```
Light.prototype.setState = function( newState ){
    this.currState = newState;
};
```
现在可以进行一些测试:
```
var light = new Light();
light.init();

```

不出意外的话，执行结果跟之前的代码一致，但是使用状态模式的好处很明显，它可以使每一种状态和它对应的行为之间的关系局部化，这些行为被分散和封装在各自对应的状态类之中，便于阅读和管理代码。

另外，状态之间的切换都被分布在状态类内部，这使得我们无需编写过多的if、else条件分支语言来控制状态之间的转换。

当我们需要为light对象增加一种新的状态时，只需要增加一个新的状态类，再稍稍改变一些现有的代码即可。假设现在light对象多了一种超强光的状态，那就先增加SuperStrongLightState类：
```
var SuperStrongLightState = function( light ){
    this.light = light;
};
SuperStrongLightState.prototype.buttonWasPressed = function(){
    console.log( '关灯' );
    this.light.setState( this.light.offLightState );
};
```
然后在Light构造函数里新增一个superStrongLightState对象：
```
var Light = function(){
    this.offLightState = new OffLightState( this );
    this.weakLightState = new WeakLightState( this );
    this.strongLightState = new StrongLightState( this );
    this.superStrongLightState = new SuperStrongLightState( this ); // 新增superStrongLightState对象
    this.button = null;
};

```
最后改变状态类之间的切换规则，从StrongLightState---->OffLightState变为StrongLightState---->SuperStrongLightState---->OffLightState：
```
StrongLightState.prototype.buttonWasPressed = function(){
    console.log( '超强光' ); // strongLightState 对应的行为
    this.light.setState( this.light.superStrongLightState ); // 切换状态到 superStrongLightState
};
```

### 状态模式的定义

通过电灯的例子，相信我们对于状态模式已经有了一定程度的了解。在回头来看GoF中对状态模式的定义：

**允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类。**

我们以逗号分割，把这句话分为两部分来看。第一部分的意思是将状态封装成独立的类，并将请求委托给当前的状态对象，当对象的内部状态改变时，会带来不同的行为变化。电灯的例子足以说明这一点，在off和on这两种不同的状态下，我们点击同一个按钮，得到的行为反馈是截然不同的。

第二部分是从客户的角度来看，我们使用的对象，在不同的状态下具有截然不同的行为，这个对象看起来是从不同的类中实例化而来的，实际上这是使用了委托的效果。

### 状态模式的通用结构

在前面的电灯例子中，我们完成了一个状态模式程序的编写。首先定义了Light类，Light类在这里也被称为上下文（Context）。随后在Light的构造函数中，我们要创建每一个状态类的实例对象，Context将持有这些状态对象的引用，以便把请求委托给状态对象。用户的请求，即点击button的动作也是实现在Context中的，代码如下：
```
var Light = function(){
    this.offLightState = new OffLightState( this ); // 持有状态对象的引用
    this.weakLightState = new WeakLightState( this );
    this.strongLightState = new StrongLightState( this );
    this.superStrongLightState = new SuperStrongLightState( this );
    this.button = null;
};
Light.prototype.init = function(){
    var button = document.createElement( 'button' ),
    self = this;
    this.button = document.body.appendChild( button );
    this.button.innerHTML = '开关';
    this.currState = this.offLightState; // 设置默认初始状态
    this.button.onclick = function(){ // 定义用户的请求动作
        self.currState.buttonWasPressed();
    }
};

```
接下来可能是个苦力活，我们要编写各种状态类，light对象被传入状态类的构造函数，状态对象也需要持有light对象的引用，以便调用light中的方法或者直接操作light对象：
```
var OffLightState = function( light ){
    this.light = light;
};
OffLightState.prototype.buttonWasPressed = function(){
    console.log( '弱光' )
    this.light.setStatee(this.light.weakLightState);
};
```
### 状态模式的优缺点

到这里我们已经学习了电灯状态模式的例子，现在是时候来总结状态模式的优缺点了。状态模式的优点如下。

- 状态模式定义了状态与行为之间的关系，并将它们封装在一个类里。通过增加新的状态类，很容易增加新的状态和转换。
- 避免Context无限膨胀，状态切换的逻辑被分布在状态类中，也去掉了Context中原本过多的条件分支。
- 用对象代替字符串来记录当前状态，使得状态的切换更加一目了然。
- Context中的请求动作和状态类中封装的行为可以非常容易地独立化而互不影响。

状态模式的缺点是会在系统中定义许多状态类，编写20个状态类是一项枯燥乏味的工作，而且系统中会因此而增加不少对象。另外，由于逻辑分散在状态类中，虽然避开了不受欢迎的条件分支语句，但也造成了逻辑分散的问题，我们无法在一个地方就看出整个状态机的逻辑。

### 状态模式中的性能优化点

在上面这个例子中，我们并没有太多地从性能方面考虑问题，实际上，这里有一些比较大的优化点。
- 有两种选择来管理state对象的创建和销毁。第一种是仅当state对象被需要时才创建并随后销毁，另一种是一开始就创建好所有的
状态对象，并且始终不销毁它们。如果state对象比较庞大，可以用第一种方式来节省内存，这样可以避免创建一些不会用到的对象
并及时地回收它们。但如果状态的改变很频繁，最好一开始就把这些state对象都创建出来，也没有必要销毁它们，因为可能很快将
再次用到它们。

- 在本章的例子中，我们为每个Context对象都创建了一组state对象，实际上这些state对象之间是可以共享的，各Context对象可
以共享一个state对象，这也是享元模式的应用场景之一

### 状态模式和策略模式的关系

状态模式和策略模式像一对双胞胎，它们都封装了一系列的算法或者行为，它们的类图看起来几乎一模一样，但在意图上有很大不同，因此它们是两种迥然不同的模式。

策略模式和状态模式的相同点是，它们都有一个上下文、一些策略或者状态类，上下文把请求委托给这些类来执行。

它们之间的区别是策略模式中的各个策略类之间是平等又平行的，它们之间没有任何联系，所以客户必须熟知这些策略类的作用，以便客户可以随时主动切换算法；而在状态模式中，状态和状态对应的行为是早已被封装好的，状态之间的切换也早被规定完成，“改变行为”这件事情发生在状态模式内部。对客户来说，并不需要了解这些细节。这正是状态模式的作用所在。

### JavaScript版本的状态机

上面例子是模拟传统面向对象语言的状态模式实现，我们为每种状态都定义一个状态子类，然后在Context中持有这些状态对象的引用，以便把currState设置为当前的状态对象。

状态模式是状态机的实现之一，但在JavaScript这种“无类”语言中，没有规定让状态对象一定要从类中创建而来。另外一点，JavaScript可以非常方便地使用委托技术，并不需要事先让一个对象持有另一个对象。下面的状态机选择了通过Function.prototype.call方法直接把请求委托给某个字面量对象来执行。

下面改写电灯的例子，来展示这种更加轻巧的做法：

```
var Light = function(){
    this.currState = FSM.off; // 设置当前状态
    this.button = null;
};
Light.prototype.init = function(){
    var button = document.createElement( 'button' ),
    self = this;
    button.innerHTML = '已关灯';
    this.button = document.body.appendChild( button );
    this.button.onclick = function(){
        self.currState.buttonWasPressed.call( self ); // 把请求委托给FSM状态机
    }
};
var FSM = {
    off: {
        buttonWasPressed: function(){
            console.log( '关灯' );
            this.button.innerHTML = '下一次按我是开灯';
            this.currState = FSM.on;
        }
    },
    on: {
        buttonWasPressed: function(){
            console.log( '开灯' );
            this.button.innerHTML = '下一次按我是关灯';
            this.currState = FSM.off;
        }
    }
};
var light = new Light();
light.init();
```

接下来尝试另外一种方法，即利用下面的delegate函数来完成这个状态机编写。这是面向对象设计和闭包互换的一个例子，前者把变量保存为对象的属性，而后者把变量封闭在闭包形成的环境中：

```
var delegate = function( client, delegation ){
    return {
        buttonWasPressed: function(){ // 将客户的操作委托给delegation对象
            return delegation.buttonWasPressed.apply( client, arguments );
        }
    }
};
var FSM = {
    off: {
        buttonWasPressed: function(){
            console.log( '关灯' );
            this.button.innerHTML = '下一次按我是开灯';
            this.currState = this.onState;
        }
    },
    on: {
        buttonWasPressed: function(){
            console.log( '开灯' );
            this.button.innerHTML = '下一次按我是关灯';
            this.currState = this.offState;
        }
    }
};
var Light = function(){
    this.offState = delegate( this, FSM.off );
    this.onState = delegate( this, FSM.on );
    this.currState = this.offState; // 设置初始状态为关闭状态
    this.button = null;
};
Light.prototype.init = function(){
    var button = document.createElement( 'button' ),
    self = this;
    button.innerHTML = '已关灯';
    this.button = document.body.appendChild( button );
    this.button.onclick = function(){
        self.currState.buttonWasPressed();
    }
};
var light = new Light();
light.init();
```

### 结语

以上例子讲解了状态模式在实际开发中的应用。状态模式也许是被大家低估的模式之一。实际上，通过状态模式重构代码之后，很多杂乱无章的代码会变得清晰。虽然状态模式一开始并不是非常容易理解，但我们有必须去好好掌握这种设计模式。

状态模式是一种特别重要的设计模式，我们一定要好好体会