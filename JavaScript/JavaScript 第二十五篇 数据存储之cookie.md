### Cookie
HTTP Cookie，通常直接叫做 cookie，最初是在客户端用于存储会话信息的。该标准要求服务器对任意 HTTP 请求发送 Set-Cookie HTTP 头作为响应的一部分，其中包含会话信息。例如，这种服务器响
应的头可能如下：

```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value
Other-header: other-header-value 

```
这个 HTTP 响应设置以 name 为名称、以 value 为值的一个 cookie，名称和值在传送时都必须是
URL 编码的。浏览器会存储这样的会话信息，并在这之后，通过为每个请求添加 Cookie HTTP 头将信
息发送回服务器，如下所示：

```
GET /index.html HTTP/1.1
Cookie: name=value
Other-header: other-header-value 

```

#### cookie的限制

cookie 在性质上是绑定在特定的域名下的。当设定了一个cookie后，再给创建它的域名发送请求时，都会包含这个cookie。这个限制确保了储存在cookie中的信息只能让批准的接受者访问，而无法被其他域访问。

由于 cookie 是存在客户端计算机上的，还加入了一些限制确保cookie不会被恶意使用，同时不会占据太多磁盘空间。每个域的cookie总数是有限的，不过浏览器之间各有不同。如下所示。

- IE6 以及更低版本限制每个域名最多 20 个 cookie。
- IE7 和之后版本每个域名最多50个。IE7最初是支持每个域名最大20个cookie，之后被微   软的一个补丁所更新。
- Firefox 限制每个域最多 50 个 cookie。
- Opera 限制每个域最多 30 个 cookie。 
- Safari 和 Chrome 对于每个域的 cookie 数量限制没有硬性规定。

当超过单个域名限制之后还要再设置 cookie，浏览器就会清除以前设置的 cookie。IE 和 Opera 会删除最近最少使用过的（LRU，Least Recently Used）cookie，腾出空间给新设置的 cookie。Firefox 看上去
好像是随机决定要清除哪个 cookie，所以考虑 cookie 限制非常重要，以免出现不可预期的后果。

浏览器中对于 cookie 的尺寸也有限制。大多数浏览器都有大约 4096B（加减 1）的长度限制。为了
最佳的浏览器兼容性，最好将整个 cookie 长度限制在 4095B（含 4095）以内。尺寸限制影响到一个域
下所有的 cookie，而并非每个 cookie 单独限制。

####  cookie 的构成
cookie 由浏览器保存的以下几块信息构成。
- **名称** ：一个唯一确定 cookie 的名称。cookie 名称是不区分大小写的，所以       myCookie 和 MyCookie被认为是同一个 cookie。然而，实践中最好将 cookie名称看作是区分大小写的，因为某些服务器会这样处理 cookie。cookie的名称必须是经过 URL 编码的。
- **值**：储存在 cookie 中的字符串值。值必须被 URL 编码。 
- **域**：cookie 对于哪个域是有效的。所有向该域发送的请求中都会包含这个 cookie 信息。这个值可以包含子域（subdomain，如www.wrox.com），也可以不包含它（如.wrox.com，则对于wrox.com的所有子域都有效）。如果没有明确设定，那么这个域会被认作来自设置 cookie 的那个域。
- **路径**：对于指定域中的那个路径，应该向服务器发送 cookie。例如，你可以指定 cookie 只有从http://www.wrox.com/books/ 中才能访问，那么 http://www.wrox.com 的页面就不会发送 cookie 信息，即使请求都是来自同一个域的。
- **失效时间**：表示cookie何时应该被删除的时间戳（也就是，何时应该停止向服务器发送这个cookie）。默认情况下，浏览器会话结束时即将所有cookie删除；不过也可以自己设置删除时间。这个值是个 GMT 格式的日期（Wdy, DD-Mon-YYYY HH:MM:SS GMT），用于指定应该删除cookie的准确时间。因此，cookie可在浏览器关闭后依然保存在用户的机器上。如果你设置的失效日期是个以前的时间，则 cookie 会被立刻删除。
- **安全标志**：指定后，cookie只有在使用SSL连接的时候才发送到服务器。例如，cookie 信息只能发送给 https://www.wrox.com，而 http://www.wrox.com 的请求则不能发送 cookie。

#### JavaScript 中的 cookie

在JavaScript中处理cookie有些复杂，因为其众所周知的蹩脚的接口，即BOM的document. cookie属性。这个属性的独特之处在于它会因为使用它的方式不同而表现出不同的行为。当用来获取属性值时，document.cookie 返回当前页面可用的（根据 cookie 的域、路径、失效时间和安全设置）所有 cookie
的字符串，一系列由分号隔开的名值对儿，如下例所示。

```
name1=value1;name2=value2;name3=value3 

```

所有名字和值都是经过 URL 编码的，所以必须使用 decodeURIComponent()来解码。

由于 JavaScript 中读写 cookie 不是非常直观，常常需要写一些函数来简化 cookie 的功能。基本的
cookie 操作有三种：读取、写入和删除。它们在 CookieUtil 对象中如下表示。

```
var CookieUtil = {
     get: function (name){
         var cookieName = encodeURIComponent(name) + "=",
         cookieStart = document.cookie.indexOf(cookieName),
         cookieValue = null;
         if (cookieStart > -1){
             var cookieEnd = document.cookie.indexOf(";", cookieStart);
             if (cookieEnd == -1){
                cookieEnd = document.cookie.length;
             }
             cookieValue = decodeURIComponent(document.cookie.substring(cookieStart
             + cookieName.length, cookieEnd));
         }
         return cookieValue;
     },
     set: function (name, value, expires, path, domain, secure) {
         var cookieText = encodeURIComponent(name) + "=" +
         encodeURIComponent(value);
         if (expires instanceof Date) {
            cookieText += "; expires=" + expires.toGMTString();
         }
         if (path) {
            cookieText += "; path=" + path;
         } 
         if (domain) {
            cookieText += "; domain=" + domain;
         }
         if (secure) {
            cookieText += "; secure";
         }
         document.cookie = cookieText;
     },
     unset: function (name, path, domain, secure){
        this.set(name, "", new Date(0), path, domain, secure);
     }
}; 
```

函数解释：

> CookieUtil.get()方法根据 cookie 的名字获取相应的值。它会在 document.cookie 字符串中查
找 cookie 名加上等于号的位置。如果找到了，那么使用 indexOf()查找该位置之后的第一个分号（表
示了该 cookie 的结束位置）。如果没有找到分号，则表示该 cookie 是字符串中的最后一个，则余下的字
符串都是 cookie 的值。该值使用 decodeURIComponent()进行解码并最后返回。如果没有发现 cookie，
则返回 null。

> CookieUtil.set()方法在页面上设置一个 cookie，接收如下几个参数：cookie 的名称，cookie 的值，
可选的用于指定 cookie 何时应被删除的 Date 对象，cookie 的可选的 URL 路径，可选的域，以及可选的
表示是否要添加 secure 标志的布尔值。参数是按照它们的使用频率排列的，只有头两个是必需的。在
这个方法中，名称和值都使用encodeURIComponent()进行了URL编码，并检查其他选项。如果expires
参数是 Date 对象，那么会使用 Date 对象的 toGMTString()方法正确格式化 Date 对象，并添加到
expires 选项上。方法的其他部分就是构造 cookie 字符串并将其设置到 document.cookie 中。

> 没有删除已有 cookie 的直接方法。所以，需要使用相同的路径、域和安全选项再次设置 cookie，并
将失效时间设置为过去的时间。CookieUtil.unset()方法可以处理这种事情。它接收 4 个参数：要删
除的 cookie 的名称、可选的路径参数、可选的域参数和可选的安全参数。

> 这些参数加上空字符串并设置失效时间为 1970 年 1 月 1 日（初始化为 0ms 的 Date 对象的值），传
给 CookieUtil.set()。这样就能确保删除 cookie。

