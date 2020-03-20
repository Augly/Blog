
### 什么是策略模式？

策略模式的定义是：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

### 使用策略模式计算奖金

很多公司的年终奖是根据员工的工资基数和年底绩效情况来发放的。例
如，绩效为S的人年终奖有4倍工资，绩效为A的人年终奖有3倍工资，
而绩效为B的人年终奖是2倍工资。假设财务部要求我们提供一段代码，
来方便他们计算员工的年终奖。

##### 不使用策略模式

我们可以编写一个名为calculateBonus的函数来计算每个人的奖金数
额。很显然，calculateBonus函数要正确工作，就需要接收两个参
数：员工的工资数额和他的绩效考核等级。代码如下：

```
var calculateBonus = function( performanceLevel, salary ){
    if ( performanceLevel === 'S' ){
        return salary * 4;
    }
    if ( performanceLevel === 'A' ){
        return salary * 3;
    }
    if ( performanceLevel === 'B' ){
        return salary * 2;
    }
};
calculateBonus( 'B', 20000 ); // 输出：40000
calculateBonus( 'S', 6000 ); // 输出：24000
```
这个代码很简单，但是也有显儿易见的缺点，比如，有很多的条件分支语句，如果我们要加一个等级必须得去修改函数内部，复用性差

现在我们来优化一下

##### 使用组合函数重构代码

一般最容易想到的办法就是使用组合函数来重构代码，我们把各种算法封装到一个个的小函数里面，这些小函数有着良好的命名，可以一目了然地知道它对应着哪种算法，它们也可以被复用在程序的其他地方。代码如下：

```
var performanceS = function( salary ){
    return salary * 4;
};
var performanceA = function( salary ){
    return salary * 3;
};
var performanceB = function( salary ){
    return salary * 2;
};
var calculateBonus = function( performanceLevel, salary ){
    if ( performanceLevel === 'S' ){
        return performanceS( salary );
    }
    if ( performanceLevel === 'A' ){
        return performanceA( salary );
    }
    if ( performanceLevel === 'B' ){
        return performanceB( salary );
    }
};
calculateBonus( 'A' , 10000 ); // 输出：30000
```
优化后的代码明显要清晰许多，但是calculateBonus函数有可能越来越庞大，而
且在系统变化的时候缺乏弹性。

接下来使用策略模式

##### 使用策略模式重构代码

策略模式指的是定义一系列的算法，把它们一个个封装起来。将不变的部分和变化的部分隔开是每个设计模式的主题，策略模式也不例外，策略模式的目的就是将算法的使用与算法的实现分离开来。

也就是说把等级和奖金倍数这些变化都单独拿出来，而最后计算年终奖的方式是不变的，把它当作一个工具来看，让等级和奖金倍数可以随意组合，只是通过工具函数实现最终结果

一个基于策略模式的程序至少由两部分组成。第一个部分是一组策略类，策略类封装了具体的算法，并负责具体的计算过程。第二个部分是环境类Context，Context接受客户的请求，随后把请求委托给某一个策略类。要做到这点，说明Context中要维持对某个策略对象的引用。

代码如下:

```
//策略类，每种等级的绩效计算方式
//s级别是4倍
var performanceS = function(){};
performanceS.prototype.calculate = function( salary ){
    return salary * 4;
};
//a级别是3倍
var performanceA = function(){};
performanceA.prototype.calculate = function( salary ){
    return salary * 3;
};
//b级别是2倍
var performanceB = function(){};
performanceB.prototype.calculate = function( salary ){
    return salary * 2;
};

//奖金类
var Bonus = function(){
    this.salary = null; // 原始工资
    this.strategy = null; // 绩效等级对应的策略对象
};
Bonus.prototype.setSalary = function( salary ){
    this.salary = salary; // 设置员工的原始工资
};
Bonus.prototype.setStrategy = function( strategy ){
    this.strategy = strategy; // 设置员工绩效等级对应的策略对象
};
Bonus.prototype.getBonus = function(){ // 取得奖金数额
    // 把计算奖金的操作委托给对应的策略对象
    return this.strategy.calculate( this.salary ); 
};

// 计算
var bonus = new Bonus();
bonus.setSalary( 10000 );
bonus.setStrategy( new performanceS() ); // 设置策略对象
console.log( bonus.getBonus() ); // 输出：40000
bonus.setStrategy( new performanceA() ); // 设置策略对象
console.log( bonus.getBonus() ); // 输出：30000
```

### JavaScript版本的策略模式

在上个例子中，我们让strategy对象从各个策略类中创建而来，这是模拟一些传统面向对象语言的实现。实际上在JavaScript语言中，函数也是对象，所以更简单和直接的做法是把strategy直接定义为函数：

```
var strategies = {
    "S": function( salary ){
        return salary * 4;
    },
    "A": function( salary ){
        return salary * 3;
    },
    "B": function( salary ){
        return salary * 2;
    }
};
var calculateBonus = function( level, salary ){
    return strategies[ level ]( salary );
};

```
这就是策略模式，函数calculateBonus只是一个计算的工具，而每种策略都是在内部完成算法，这样只需要把策略和每种策略对应的工资传入就可以得出年终奖是多少

这里的calculateBonus只是纯粹是一个计算工具类


### 重点！用策略模式去实现表单验证

表单验证我们经常会用到，一般我们写表单验证的方法如下:

```
<html>
<body>
<form action="http:// xxx.com/register" id="registerForm" method="post">
    请输入用户名：<input type="text" name="userName"/ >
    请输入密码：<input type="text" name="password"/ >
    请输入手机号码：<input type="text" name="phoneNumber"/ >
<button>提交</button>
</form>
<script>
var registerForm = document.getElementById( 'registerForm' );
registerForm.onsubmit = function(){
    if ( registerForm.userName.value === '' ){
        alert ( '用户名不能为空' );
        return false;
    }
    if ( registerForm.password.value.length < 6 ){
        alert ( '密码长度不能少于6位' );
        return false;
    }
    if ( !/(^1[3|5|8][0-9]{9}$)/.test( registerForm.phoneNumber.value ) ){
        alert ( '手机号码格式不正确' );
        return false;
    }
}
</script>
</body>
</html>

```
这样的代码是面对过程编程，也是最显而易懂的方式，这样的代码可复用性差，每添加一个表单项都需要去写一个if语句，那么有没有好的方式呢？

**让我们用策略模式来优化改写吧！**

### 用策略模式重构表单校验

下面我们将用策略模式来重构表单校验的代码
#### 第一步我们要把这些校验逻辑都封装成策略对象：

```
var strategies = {
    isNonEmpty: function( value, errorMsg ){ // 不为空
        if ( value === '' ){
            return errorMsg ;
        }
    },
    minLength: function( value, length, errorMsg ){ // 限制最小长度
        if ( value.length < length ){
            return errorMsg;
        }
    },
    isMobile: function( value, errorMsg ){ // 手机号码格式
        if ( !/(^1[3|5|8][0-9]{9}$)/.test( value ) ){
            return errorMsg;
        }
    }
};

```
#### 第二步添加校验规则并使用：

```
var validataFunc = function(){
    var validator = new Validator(); // 创建一个validator对象
    /***************添加一些校验规则****************/
    validator.add( registerForm.userName, 'isNonEmpty', '用户名不能为空' );
    validator.add( registerForm.password, 'minLength:6', '密码长度不能少于6位' );
    validator.add( registerForm.phoneNumber, 'isMobile', '手机号码格式不正确' );
    var errorMsg = validator.start(); // 获得校验结果
    return errorMsg; // 返回校验结果
}
var registerForm = document.getElementById( 'registerForm' );
registerForm.onsubmit = function(){
    var errorMsg = validataFunc(); // 如果errorMsg有确切的返回值，说明未通过校验
    if ( errorMsg ){
        alert ( errorMsg );
        return false; // 阻止表单提交
    }
};
```

#### 最后一步是创建一个校验器Validator类:

```
var Validator = function(){
    this.cache = []; // 保存校验规则
};
Validator.prototype.add = function( dom, rule, errorMsg ){
    var ary = rule.split( ':' ); // 把strategy和参数分开
    this.cache.push(function(){ // 把校验的步骤用空函数包装起来，并且放入cache
        var strategy = ary.shift(); // 用户挑选的strategy
        ary.unshift( dom.value ); // 把input的value添加进参数列表
        ary.push( errorMsg ); // 把errorMsg添加进参数列表
        return strategies[ strategy ].apply( dom, ary );
    });
};
Validator.prototype.start = function(){
    for ( var i = 0, validatorFunc; validatorFunc = this.cache[ i++ ]; ){
        var msg = validatorFunc(); // 开始校验，并取得校验后的返回信息
        if ( msg ){ // 如果有确切的返回值，说明校验没有通过
            return msg;
        }
    }
};

```

这样用策略模式优化的表单校验，只需要增加校验的规则即可，可复用性更强

#### 对一个表单项进行多条规则校验

如何做呢？

代码如下:

```
//只需要在给表单项添加规则的时候添加一个数组
validator.add( registerForm.userName, [{
    strategy: 'isNonEmpty',
    errorMsg: '用户名不能为空'
}, {
    strategy: 'minLength:6',
    errorMsg: '用户名长度不能小于10位'
}] );

//然后修改一下校验器的添加方法
Validator.prototype.add = function( dom, rules ){
    var self = this;
    //循环调用
    for ( var i = 0, rule; rule = rules[ i++ ]; ){
        (function( rule ){
        var strategyAry = rule.strategy.split( ':' );
            var errorMsg = rule.errorMsg;
            self.cache.push(function(){
                var strategy = strategyAry.shift();
                strategyAry.unshift( dom.value );
                strategyAry.push( errorMsg );
                return strategies[ strategy ].apply( dom, strategyAry );
            });
        })( rule )
    }
};

```

现在好多框架的表单校验器都是基于策略模式进行创建的

### 策略模式的优缺点

策略模式是一种常用且有效的设计模式，从上面例子我们可以总结出策略模式的一些优点。

- 策略模式利用组合、委托和多态等技术和思想，可以有效地避免多重条件选择语句。
- 策略模式提供了对开放—封闭原则的完美支持，将算法封装在独立的strategy中，使得它们易于切换，易于理解，易于扩展。
- 策略模式中的算法也可以复用在系统的其他地方，从而避免许多重复的复制粘贴工作。
- 在策略模式中利用组合和委托来让Context拥有执行算法的能力，这也是继承的一种更轻便的替代方案。

缺点:

- 使用策略模式会在程序中增加许多策略类或者策略对象,代码语句可能会增加
- 使用策略模式必须要知道所有的策略点，并且会把所有的策略算法暴露出来，这是违法最少知识原则的。


