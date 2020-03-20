
Web Storage 最早是在 Web 超文本应用技术工作组（WHAT-WG）的 Web 应用 1.0 规范中描述的。

这个规范的最初的工作最终成为了 HTML5 的一部分。Web Storage 的目的是克服由 cookie 带来的一些限制，当数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。Web Storage 的两个主要目标是： 

- 提供一种在 cookie 之外存储会话数据的途径；
- 提供一种存储大量可以跨会话存在的数据的机制。

###  Storage 类型 
Storage 类型提供最大的存储空间（因浏览器而异）来存储名值对儿。Storage 的实例与其他对
象类似，有如下方法。

- clear()： 删除所有值；Firefox 中没有实现 。
- getItem(name)：根据指定的名字 name 获取对应的值。
- key(index)：获得 index 位置处的值的名字。
- removeItem(name)：删除由 name 指定的名值对儿。
- setItem(name, value)：为指定的 name 设置一个对应的值。

> Storage 类型只能存储字符串。非字符串的数据在存储之前会被转换成字符串。

###  sessionStorage 对象

sessionStorage 对象存储特定于某个会话的数据，也就是该数据只保持到浏览器关闭。这个对象
就像会话 cookie，也会在浏览器关闭后消失。存储在 sessionStorage 中的数据可以跨越页面刷新而
存在，同时如果浏览器支持，浏览器崩溃并重启之后依然可用（Firefox 和 WebKit 都支持，IE 则不行）。

因为 seesionStorage 对象绑定于某个服务器会话，所以当文件在本地运行的时候是不可用的。存
储在 sessionStorage 中的数据只能由最初给对象存储数据的页面访问到，

IE8的强制写入：
```
//只适用于 IE8
sessionStorage.begin();
sessionStorage.name = "Nicholas";
sessionStorage.book = "Professional JavaScript";
sessionStorage.commit(); 

```

###  globalStorage 对象

作为最初的 Web Storage规范的一部分，这个对象的目的是跨越会话存储数据，但有特定的访问限制。要使用globalStorage，首先要指定哪些域可以访问该数据。可以通过方括号标记使用属性来实现，如以下例子所示。

```
//保存数据
globalStorage["wrox.com"].name = "Nicholas";
//获取数据
var name = globalStorage["wrox.com"].name; 

```
在这里，访问的是针对域名 wrox.com 的存储空间。globalStorage 对象不是 Storage 的实例，
而具体的 globalStorage["wrox.com"]才是。这个存储空间对于 wrox.com 及其所有子域都是可以
访问的。可以像下面这样指定子域名。

```
//保存数据
globalStorage["www.wrox.com"].name = "Nicholas";
//获取数据
var name = globalStorage["www.wrox.com"].name; 

```

对 globalStorage 空间的访问，是依据发起请求的页面的域名、协议和端口来限制的。例如，如
果使用 HTTPS 协议在 wrox.com 中存储了数据，那么通过 HTTP 访问的 wrox.com 的页面就不能访问
该数据。同样，通过 80 端口访问的页面则无法与同一个域同样协议但通过 8080 端口访问的页面共享数
据。这类似于 Ajax 请求的同源策略


如果不使用 removeItem() 或 者 delete 删除，或者用户未清除浏览器缓存，存储在
globalStorage 属性中的数据会一直保留在磁盘上。这让 globalStorage 非常适合在客户端存储文
档或者长期保存用户偏好设置。

### localStorage 对象 

localStorage 对象在修订过的 HTML 5 规范中作为持久保存客户端数据的方案取代了
globalStorage。与 globalStorage 不同，不能给 localStorage 指定任何访问规则；规则事先就
设定好了。要访问同一个 localStorage 对象，页面必须来自同一个域名（子域名无效），使用同一种
协议，在同一个端口上。这相当于 globalStorage[location.host]。


由于 localStorage 是 Storage 的实例，所以可以像使用 sessionStorage 一样来使用它。


### storage 事件 

 Storage 对象进行任何修改，都会在文档上触发storage事件。当通过属性或setItem()方法保存数据，使用 delete操作符或removeItem()删除数据，或者调用clear()方法时，都会发生该事件。这个事件的 event 对象有以下属性。
 
 - domain：发生变化的存储空间的域名。
 - key：设置或者删除的键名。
 - newValue：如果是设置值，则是新值；如果是删除键，则是 null。
 - oldValue：键被更改之前的值。
 

## IndexedDB

web中的数据库！

Indexed Database API，或者简称为 IndexedDB，是在浏览器中保存结构化数据的一种数据库。
IndexedDB 是为了替代目前已被废弃的 Web SQL Database API（因为已废弃，所以本书未介绍）而出现
的。IndexedDB 的思想是创建一套 API，方便保存和读取 JavaScript 对象，同时还支持查询及搜索。

IndexedDB 设计的操作完全是异步进行的。因此，大多数操作会以请求方式进行，但这些操作会在
后期执行，然后如果成功则返回结果，如果失败则返回错误。差不多每一次 IndexedDB 操作，都需要你
注册 onerror 或 onsuccess 事件处理程序，以确保适当地处理结果。


创建：
```
var indexedDB = window.indexedDB || window.msIndexedDB || window.mozIndexedDB ||
window.webkitIndexedDB; 
```

#### 打开或者创建一个IndexedDB
```
var request, database;
request = indexedDB.open("admin");
request.onerror = function(event){
     alert("Something bad happened while trying to open: " +
     event.target.errorCode);
};
request.onsuccess = function(event){
    database = event.target.result;
}; 

```

在这两个事件处理程序中，event.target 都指向 request 对象，因此它们可以互换使用。如果响
应的是 onsuccess 事件处理程序，那么 event.target.result 中将有一个数据库实例对象（IDBDatabase），这个对象会保存在 database 变量中。如果发生了错误，那 event.target.errorCode 中将
保存一个错误码，表示问题的性质。以下就是可能的错误码（这个错误码适合所有操作）。

- IDBDatabaseException.UNKNOWN_ERR(1)：意外错误，无法归类。
- IDBDatabaseException.NON_TRANSIENT_ERR(2)：操作不合法。
- IDBDatabaseException.NOT_FOUND_ERR(3)：未发现要操作的数据库。
- IDBDatabaseException.CONSTRAINT_ERR(4)：违反了数据库约束。
- IDBDatabaseException.DATA_ERR(5)：提供给事务的数据不能满足要求。
- IDBDatabaseException.NOT_ALLOWED_ERR(6)：操作不合法。
- IDBDatabaseException.TRANSACTION_INACTIVE_ERR(7)：试图重用已完成的事务。
- IDBDatabaseException.ABORT_ERR(8)：请求中断，未成功。
- IDBDatabaseException.READ_ONLY_ERR(9)：试图在只读模式下写入或修改数据。
- IDBDatabaseException.TIMEOUT_ERR(10)：在有效时间内未完成操作。
- IDBDatabaseException.QUOTA_ERR(11)：磁盘空间不足。

默认情况下，IndexedDB 数据库是没有版本号的，最好一开始就为数据库指定一个版本号。为此，
可以调用 setVersion()方法，传入以字符串形式表示的版本号。同样，调用这个方法也会返回一个请
求对象，需要你再指定事件处理程序。

```
if (database.version != "1.0"){
     request = database.setVersion("1.0");
     request.onerror = function(event){
         alert("Something bad happened while trying to set version: " +
         event.target.errorCode);
     };
     request.onsuccess = function(event){
         alert("Database initialization complete. Database name: " + database.name +
         ", Version: " + database.version);
     };
} else {
     alert("Database already initialized. Database name: " + database.name +
     ", Version: " + database.version);
} 
```

####  对象存储空间

在建立了与数据库的连接之后，下一步就是使用对象存储空间①。如果数据库的版本与你传入的版
本不匹配，那可能就需要创建一个新的对象存储空间。在创建对象存储空间之前，必须要想清楚你想要
保存什么数据类型。

假设你要保存的用户记录由用户名、密码等组成，那么保存一条记录的对象应该类似如下所示：

```
var user = {
     username: "007",
     firstName: "James",
     lastNa me: "Bond",
     password: "foo"
}; 

var store = db.createObjectStore("users", { keyPath: "username" }); 

```

####  事务

跨过创建对象存储空间这一步之后，接下来的所有操作都是通过事务来完成的。在数据库对象上调
用 transaction()方法可以创建事务。任何时候，只要想读取或修改数据，都要通过事务来组织所有
操作。在最简单的情况下，可以像下面这样创建事务①。

var transaction = db.transaction(); 

如前所述，这些事务都是以只读方式访问数据。要修改访问方式，必须在创建事务时传入第二个参
数，这个参数表示访问模式，用 IDBTransaction 接口定义的如下常量表示：READ_ONLY（0）表示只
读，READ_WRITE（1）表示读写，VERSION_CHANGE（2）表示改变。IE10+和 Firefox 4+实现的是
IDBTransaction，但在 Chrome 中则叫 webkitIDBTransaction，所以使用下面的代码可以统一接口：

var IDBTransaction = window.IDBTransaction || window.webkitIDBTransaction; 

var transaction = db.transaction("users", IDBTransaction.READ_WRITE); 

取得了事务的索引后，使用 objectStore()方法并传入存储空间的名称，就可以访问特定的存储
空间。然后，可以像以前一样使用 add()和 put()方法，使用 get()可以取得值，使用 delete()可以
删除对象，而使用 clear()则可以删除所有对象。get()和 delete()方法都接收一个对象键作为参数，
而所有这 5 个方法都会返回一个新的请求对象。例如：

示例:
```
var request = db.transaction("users").objectStore("users").get("007");
request.onerror = function(event){
 alert("Did not get the object!");
};
request.onsuccess = function(event){
 var result = event.target.result;
 alert(result.firstName); //"James"
}; 

```



