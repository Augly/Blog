### 什么是单例模式?

> 单例模式的定义是：保证一个类仅有一个实例，并提供一个访问它的全局访问点

单例模式是一种常用的模式，有一些对象往往我们只需要哦一个，比如线程池，全局缓存，浏览器中的Windows对象等。

在JavaScript中，单例模式的用途非常广泛，试想一下，当我们单击登陆按钮的时候，页面上会出现一个悬浮框，并且这个悬浮框是唯一的，无论点击多少次登陆按钮，这个悬浮窗只会出现一次，那么这个登陆浮窗就适合用单例模式来创建

### 实现单例模式

要实现一个标准的单例模式并不复杂，无非是用一个变量来标志当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例时候，直接返回之前创建过的对象。

代码如下：
```
var Singleton = function (name) {
  this.name = name;
  this.instance = null
}
Singleton.prototype.getName = function () {
  alert(this.name)
}

Singleton.getInstance = function (name) {
  if (!this.instance) {
    this.instance = new Singleton(name)
  }
  return this.instance
}
var a = Singleton.getInstance('seven1')
var b = Singleton.getInstance('seven2')

a===b //true
```
或者：
```
var Singleton = function (name) {
  this.name = name;
}
Singleton.prototype.getName = function () {
  alert(this.name)
}

Singleton.getInstance = (function () {
  var instance = null
  return function () {
    if (!instance) {
      instance = new Singleton(name)
    }
    return instance
  }
})()
var a = Singleton.getInstance('seven1')
var b = Singleton.getInstance('seven2')

alert(a === b)
```
我们通过Singleton.getInstance来获取Singleton类的唯一对象，这种方式相对简单，但有一个问题，就是增加了这个类的 "不透明性"，Singleton类的使用者必须知道这是一个单例类，跟以往通过new XXX的方式来获取对象不同，这里偏要使用Singleton,getInstance来获取对象。

#### 透明的单例模式

我们现在的目标是实现一个"透明"的单例类，用户可以从这个类创建对象的时候，可以像使用其它任何普通类一样。在下面的例子中，我们将使用CreateDiv单例类，它的作用是负责在页面中创建唯一的div节点

代码如下：

```
var CreateDiv = (function () {
  var instance;
  var CreateDiv = function (html) {
    if (instance) {
      return instance;
    }
    this.html = html;
    thid.init()
    return instance = this
  }
  CreateDiv.prototype.init = function () {
    var div = document.createElement('div')
    div.innerHTML = this.html
    document.body.appendChild(div)
  }
  return CreateDiv
})()

```

#### 用代理实现单例模式

现在我们通过引入代理类的方式

```
// 代理类模式

var CreateDiv = function (html) {
  this.html = html;
  this.init();
}
CreateDiv.prototype.init = function () {
  var div = document.createElement('div')
  div.innerHTML = this.html;
  document.body.appendChild(div)
}

//代理类

var ProxySingletonCreateDiv = (function () {
  var instance;
  return function (html) {
    if (!instance) {
      instance = new CreateDiv(html)
    }
    return instance;
  }
})()

var a = new ProxySingletonCreateDiv('sven1')
var b = new ProxySingletonCreateDiv('sven2')
```
通过引入代理类的方式，我们同样完成了一个单例模式的编写，跟之前不同的是，现在我们把负责管理单例的逻辑移到了代理类ProxySingletonCreateDiv中，这样一来CreateDiv就变成一个普通的类，跟ProxySingletonCreateDiv组合起来就能达到单例模式的效果