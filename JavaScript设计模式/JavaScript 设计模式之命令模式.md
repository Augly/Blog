### 什么是命令模式？

假设有一个快餐店，而我是该餐厅的点餐服务员，那么我一天的工作应该是这样的：当某位客人点餐或者打来订餐电话后，我会把他的需求都写在清单上，然后交给厨房，客人不用关心是哪些厨师帮他炒菜。我们餐厅还可以满足客人需要的定时服务，比如客人可能当前正在回家的路上，要求1个小时后才开始炒他的菜，只要订单还在，厨师就不会忘记。客人也可以很方便地打电话来撤销订单。另外如果有太多的客人点餐，厨房可以按照订单的顺序排队炒菜。

这些记录着订餐信息的清单，便是命令模式中的命令对象，这种模式就是命令模式

#### 命令模式的用途

命令模式是最简单和优雅的模式之一，命令模式中的命令（command）指的是一个执行某些特定事情的指令。

命令模式最常见的应用场景是：有时候需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是什么。此时希望用一种松耦合的方式来设计程序，使得请求发送者和请求接收者能够消除彼此之间的耦合关系。

拿订餐来说，客人需要向厨师发送请求，但是完全不知道这些厨师的名字和联系方式，也不知道厨师炒菜的方式和步骤。命令模式把客人订餐的请求封装成command对象，也就是订餐中的订单对象。这个对象可以在程序中被四处传递，就像订单可以从服务员手中传到厨师的手中。这样一来，客人不需要知道厨师的名字，从而解开了请求调用者和请求
接收者之间的耦合关系。

另外，相对于过程化的请求调用，command对象拥有更长的生命周期。对象的生命周期是跟初始请求无关的，因为这个请求已经被封装在了command对象的方法中，成为了这个对象的行为。我们可以在程序运行的任意时刻去调用这个方法，就像厨师可以在客人预定1个小时之后才帮他炒菜，相当于程序在1个小时之后才开始执行command对象的方法。

除了这两点之外，命令模式还支持撤销、排队等操作

### 命令模式的例子——菜单程序

假设我们正在编写一个用户界面程序，该用户界面上至少有数十个Button按钮。因为项目比较复杂，所以我们决定让某个程序员负责绘制这些按钮，而另外一些程序员则负责编写点击按钮后的具体行为，这些行为都将被封装在对象里。

我们很快可以找到在这里运用命令模式的理由：点击了按钮之后，必须向某些负责具体行为的对象发送请求，这些对象就是请求的接收者。但是目前并不知道接收者是什么对象，也不知道接收者究竟会做什么。此时我们需要借助命令对象的帮助，以便解开按钮和负责具体行为对象之间的耦合。

设计模式的主题总是把不变的事物和变化的事物分离开来，命令模式也不例外。按下按钮之后会发生一些事情是不变的，而具体会发生什么事情是可变的。通过command对象的帮助，将来我们可以轻易地改变这种关联，因此也可以在将来再次改变按钮的行为。

首先，进行绘制

```
<body>
    <button id="button1">点击按钮1</button>
    <button id="button2">点击按钮2</button>
    <button id="button3">点击按钮3</button>
</body>
<script>
var button1 = document.getElementById( 'button1' ),
var button2 = document.getElementById( 'button2' ),
var button3 = document.getElementById( 'button3' );
</script>

```

然后定义setCommand（设置命令）函数

setCommand函数负责往按钮上面安装命令。可以肯定的是，点击按钮会执行某个command命令，执行命令的动作被约定为调用command对象的execute()方法。虽然还不知道这些命令究竟代表什么操作，但负责绘制按钮的程序员不关心这些事情，他只需要预留好安装命令的接口，command对象自然知道如何和正确的对象

```
var serCommand=function(button,command){
    button.onclick=function(){
        command.execute()
    }
}
```

最后，负责编写点击按钮之后的具体行为的程序员总算交上了他们的成果，他们完成了刷新菜单界面、增加子菜单和删除子菜单这几个功能，这几个功能被分布在MenuBar和SubMenu这两个对象中：

```
var MenuBar = {
    refresh: function(){
        console.log( '刷新菜单目录' );
    }
};
var SubMenu = {
    add: function(){
        console.log( '增加子菜单' );
    },
    del: function(){
        console.log( '删除子菜单' );
    }
};

```
在让button变得有用起来之前，我们要先把这些行为都封装在命令类中：
```
var RefreshMenuBarCommand = function( receiver ){
    this.receiver = receiver;
};
RefreshMenuBarCommand.prototype.execute = function(){
    this.receiver.refresh();
};
var AddSubMenuCommand = function( receiver ){
    this.receiver = receiver;
};
AddSubMenuCommand.prototype.execute = function(){
    this.receiver.add();
};
var DelSubMenuCommand = function( receiver ){
    this.receiver = receiver;
};
DelSubMenuCommand.prototype.execute = function(){
    console.log( '删除子菜单' );
};
```

最后就是把命令接收者传入到command对象中，并且把command对象安装到button上面：

```
var refreshMenuBarCommand = new RefreshMenuBarCommand( MenuBar );
var addSubMenuCommand = new AddSubMenuCommand( SubMenu );
var delSubMenuCommand = new DelSubMenuCommand( SubMenu );
setCommand( button1, refreshMenuBarCommand );
setCommand( button2, addSubMenuCommand );
setCommand( button3, delSubMenuComman
```

### JavaScript中的命令模式

也许我们会感到很奇怪，所谓的命令模式，看起来就是给对象的某个方法取了execute的名字。引入command对象和receiver这两个无中生有的角色无非是把简单的事情复杂化了，即使不用什么模式，用下面寥寥几行代码就可以实现相同的功能：

```
var bindClick = function( button, func ){
    button.onclick = func;
};
var MenuBar = {
    refresh: function(){
        console.log( '刷新菜单界面' );
    }
};
var SubMenu = {
    add: function(){
        console.log( '增加子菜单' );
    },
    del: function(){
        console.log( '删除子菜单' );
    }
};
bindClick( button1, MenuBar.refresh );
bindClick( button2, SubMenu.add );
bindClick( button3, SubMenu.del );
```

命令模式的由来，其实是回调（callback）函数的一个面向对象的替代品。

#### 撤销命令

命令模式的作用不仅是封装运算块，而且可以很方便地给命令对象增加撤销操作。就像订餐时客人可以通过电话来取消订单一样。

撤销操作的实现一般是给命令对象增加一个名为unexecude或者undo的方法，在该方法里执行execute的反向操作。

#### 撤消和重做

很多时候，我们需要撤销一系列的命令。比如在一个围棋程序中，现在已经下了10步棋，我们需要一次性悔棋到第5步。在这之前，我们可以把所有执行过的下棋命令都储存
在一个历史列表中，然后倒序循环来依次执行这些命令的undo操作，直到循环执行到第5个命令为止。

然而，在某些情况下无法顺利地利用undo操作让对象回到execute之前的状态。比如在一个Canvas画图的程序中，画布上有一些点，我们在这些点之间画了N条曲线把这些点相互连接起来，当然这是用命令模式来实现的。但是我们却很难为这里的命令对象定义一个擦除某条曲线的undo操作，因为在Canvas画图中，擦除一条线相对不容易实现。

这时候最好的办法是先清除画布，然后把刚才执行过的命令全部重新执行一遍，这一点同样可以利用一个历史列表堆栈办到。记录命令日志，然后重复执行它们，这是逆转不可逆命令的一个好办法。

#### 命令队列

在订餐的故事中，如果订单的数量过多而厨师的人手不够，则可以让这些订单进行排队处理。第一个订单完成之后，再开始执行跟第二个订单有关的操作。

#### 宏命令

宏命令是一组命令的集合，通过执行宏命令的方式，可以一次执行一批命令。



