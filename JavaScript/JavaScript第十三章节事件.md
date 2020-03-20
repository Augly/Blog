### 事件
-   事件流：描述的是从页面中接收事件的顺序
    -   事件冒泡：事件开始时由最具体的元素接收，然后逐级向上传播到较为不具体的节点
    -   事件捕获：由不太具体的节点应该更早得接收到事件，而最具体的节点应该最后接收到事件
-   DOM事件流：事件捕获阶段，处于目标阶段，事件冒泡阶段

### 事件处理程序
-   Html事件处理程序
-   DOM2级事件处理程序
    -   addEventListener(eventName,methon,Boolean)：接收三个参数，第一个为需要监听的事件名，第二个参数为执行方法，第三个参数为是否在事件捕获阶段调用事件处理程序，优点是可以添加多个事件处理程序
    -   removeEventListener()：移除通过addEventListener注册的事件处理程序
-   IE事件处理程序
    -   attachEvent(eventName,methond)：
    -   detachEvent：移除事件处理程序
    
### 事件对象
-   DOM中的事件对象：event对象
    -   bubbles：Boolean，表示事件是否可以冒泡
    -   cancelable：Boolean，表示是否可以取消事件的默认行为
    -   currenTarget：Boolean，表示其事件处理程序当前正在处理事件的哪个元素
    -   defaultPrevented：为true表示已经调用了preventDefault()
    -   detail：与事件相关的细节信息
    -   eventPhase：调用事件处理程序的阶段：1表示捕获阶段，2表示处于目标，3表示冒泡阶段
    -   preventDefault()：取消事件的默认行为，如果ancelable是true，则可以使用这个方法
    -   stopImmediatePropagation()：取消事件的进一步捕获或冒泡，同时阻止任何事件处理程序被调用
    -   stopPropagation()：取消事件的进一步捕获或者冒泡，如果bubbles为true，则可以使用这个方法
    -   target：事件的目标
    -   trusted：为true表示事件是浏览器生成的，为false表示事件是由开发人员创建的
    -   type：被触发的事件类型
    -   view：与事件关联的抽象视图，等同于发生事件的window对象
    -   在事件处理程序内部，对象this始终等于currenTarget的值，而target则只包含事件的实际目标。
-   IE中的事件对象
    -   cancelBubble：默认值为false，但将其设置为true就可以取消事件冒泡
    -   returnValue：默认值为true，但将其设置为false就可以取消事件的默认行为
    -   srcElement：事件的目标
    -   type：被触发的事件的类型


### 事件类型

> 事件类型有以下几类:UI事件，焦点事件，鼠标事件，滚轮事件，文本事件，键盘事件，合成事件，变动事件，变动名称事件

-   UI事件
    -   DOMActivate：表示DOM元素已经被用户操作，DOM3已废弃
    -   load：当页面完全加载后在window上面触发，当所有框架都加载完毕时在框架上面触发，当图像加载完毕时在img标签上触发
    -   unload：当页面完全卸载后在window上面触发，当所有框架都卸载后在框架集上面触发
    -   abort：在用户停止下载过程时，如果嵌入的内容没有完全加载完，则在<object>元素上面触发
    -   error：当发生JavaScript错误时，在window上面触发，当无法加载图像时候在<img>上面触发
    -   select：当用户选择文本框中的一或多个字符时触发
    -   resize：当窗口或者框架的大小变化时在window触发或者框架上面触发
    -   scroll：当用户滚动带滚动条的元素中的内容时，在该元素上面触发，<body>元素上面包含所加载页面的滚动条
-   焦点事件
    -   blur：在元素失去焦点时触发，这个事件不会冒泡，所有浏览器都支持它
    -   DOMFocusIn：在元素获得焦点时触发，只有Opera支持这个事件，DOM3已废弃
    -   DOMFocusOut：在元素失去焦点时触发，只有Opera支持这个事件，DOM3已废弃
    -   focus：在元素获得焦点时触发，这个事件不会冒泡，所有浏览器都支持它
    -   focusin：在元素获得焦点时触发，与focus等价，但它冒泡，所有浏览器都支持
    -   focusout：在元素失去焦点时触发，与blur等价，所有浏览器都支持
-   鼠标与滚轮事件
    -   click：在用户单击主鼠标按钮时触发，或者按下回车键触发
    -   dbclick：在用户双击鼠标按钮时触发，
    -   mousedown：在用户按下任意鼠标按钮时触发，不能通过键盘触发这个事件
    -   mouseenter：在鼠标光标从元素外部首次移动到元素内部范围内触发，这个事件不冒泡，DOM3支持
    -   mouseleave：在位于元素上方的光标移动到元素范围之外时触发。这个事件不冒泡，并且在光标移动到后代元素上不会触发。DOM3支持
    -   mousemove：当鼠标指针在元素内部移动时重复地触发。不能通过键盘触发这个事件
    -   mouseout：当鼠标指针位一个元素上方，然后用户将其移入到另一个元素是触发。有移入的另一个元素可能位于前一个元素的外部，也可能是这个元素的子元素。不能通过键盘触发这个事件
    -   mouseup：在用户释放鼠标按钮时触发，不能通过键盘触发这个事件
    -   基本事件触发的顺序如下：mousedown，mouseup,click,mousedown,mouseup,click,dbclick,ie8以及之前的版本会跳过第二个mousedown


-   鼠标滚动坐标
    -   clientX和clienY：相对于浏览器客户区的坐标位置
    -   pageX和pageY：相对于页面的坐标位置，在页面没有滚动的情况下，pageX和pageY与clientX和clientY的值相等
    -   screenX和screenY：相对于整个屏幕的坐标信息

-   修改键
    -   shiftKey，ctrlKey，altKey，metaKey

-   鼠标按钮(event.button)
    -   0：表示没有按下按钮
    -   1：表示按下了著鼠标按钮
    -   2：表示按下了次鼠标按钮
    -   3：表示同时按下了主，次鼠标按钮
    -   4：表示按下了中间的鼠标按钮
    -   5：表示同时按下了主鼠标按钮和中间的鼠标按钮
    -   6：表示同时按下了次鼠标按钮和中间的鼠标按钮
    -   7：表示同时按下了三个鼠标按钮

-  更多的事件信息
    -  altLeft：表示是否按下alt键
    -  ctrlLeft：表示是否按下了ctrl键
    -  offsetX：光标相对于目标元素边界的X坐标
    -  offsetY：光标相对于目标元素边界的Y坐标
    -  shiftLeft：表示是否按下了Shift键

-   鼠标滚动事件
    -  mousewheel事件：当鼠标滚轮向前滚动时wheelDelta是120的倍数，当鼠标滚轮向后滚动时是-120的倍数，在Opera中需要做兼容处理
-   键盘与文本事件
    -  keydown：按下键盘任意键时触发
    -  keypress：按下键盘字符键时触发
    -  keyup：当释放键盘时触发事件
    -  textInput：在文本插入文本框之前会触发 
-   DOM3中textInput事件（event.data）
    -  0：表示浏览器不确定怎么输入的
    -  1：表示是使用键盘输入的
    -  2：表示文本是粘贴进来的
    -  3：表示文本是拖放进来的
    -  4：表示文本时使用IME输入的
    -  5：表示文本是通过在表单中选择一项输入的
    -  6：表示文本是通过手输入的
    -  7：表示文本是通过语音输入的
    -  8：表示文本是通过几种方式组合输入的
    -  9：表示文本是通过脚本输入的
        
-  复合事件
    - compositionsstart：表示要开始输入了
    - compositionupdate：在向输入字段中插入新字符时触发
    - compositionend：表示返回正常键盘输入状态

-   变动事件
    - DOMSubtreeModified：在DOM结构中发生任何变化时触发。这个事件在其他任何事件触发后都会触发
    - DOMNodeInserted：在一个节点作为子节点被插入到另一个节点中时触发
    - DOMNodeRemoved：在节点从其父级节点中被移除时触发
    - DOMNodeInsertedIntoDocument：在一个节点被直接插入到文档或通过子树间接插入文档之后触发，这个事件在DOMNodeInserted之后触发
    - DOMNodeRemoveFromDocument：在一个节点被直接从文档移除或通过子树间接从文档中移除之前触发。这个事件在DOMNodeRemoved之后触发
    - DOMAttrModified：在特性被修改之后触发
    - DOMCharacterDataModified：在文本节点的值发生变化时触发
-   HTML5事件
    - contextmenu事件：鼠标上下文菜单事件，需要先阻止默认上下文
    - beforeunload事件：页面卸载前触发
    - DOMContentLoaded事件：在形成完整的DOM树之后触发，一般在load之前触发
    - readystatechange事件：提供文本或元素的加载状态
        - uninitialized：对象存在但未初始化
        - loading：对象正在加载数据
        - loaded：对象加载数据完成
        - interactive：可以操作对象了，但还没有完全加载
        - complete：对象已经加载完毕
    - pageshow和pagehide事件
    - hashchange事件：URL参数发生变化时触发
-   设备事件
    - orientationchange事件：window.orientation有三个值
        - 0：肖像模式
        - 90：表示向左旋转的横向模式
        - -90：表示向右旋转的横向模式
    - MozOrientation事件：
        -x：
        -y：
        -z：
    - deviceorientation事件
        - alpha：在围绕z轴旋转时，y轴的度数差，是一个介于0到360度之间的浮点数
        - beta：在围绕x轴旋转时，z轴的度数差，是一个介于-180到180之间的浮点数
        - gamma：在围绕y轴旋转时，z轴的度数差，是一个介于-90到90之间的浮点数，
        - absolute：布尔值，表示设备是否返回一个绝对值
        - compassCalibrated：布尔值，表示设备的指南针是否校准过
    - devicemotion事件
        - acceleration：一个包含x，y，z属性的对象，在不考虑重力的情况下，告诉你在每个方向上的加速度
        - accelerationIncludingGravity：一个包含x，y和z属性的对象，在考虑z轴自然重力加速度的情况下，每个方向的加速度
        - interval：以毫秒表示的时间值，必须在另一个devicemotion事件之前传入，这个值在每个事件中应该是一个常量
    
    
-   触摸与手势事件
    -    触摸事件
            - touchstart：当手指触摸屏幕时触发，即使已经有了一个手指放在了屏幕上也会触发
            - touchmove：当手指在屏幕上滑动时连续地触发。在事件发生期间调用preventDefault（）可以阻止滚动
            - touchend：当手指从屏幕上移开时触发
            - touchcancel：当系统停止跟踪触摸时触发
            - touches：表示当前跟踪的触摸操作的Touch对象的数组
            - targetTouchs：特定事件目标的Touch对象的数组
            - changeTouches：表示自上次触摸事件以来发生了什么改变的Touch对象的数组
            - Touch对象都包含以下属性
                - clientX：触摸目标在视口中的x坐标
                - clientY：触摸目标在视口中的y坐标
                - identifier：标识触摸的唯一ID
                - pageX：触摸目标在页面中的x坐标
                - pageY：触摸目标在页面中的y坐标
                - screenX：触摸目标在屏幕中的X坐标
                - screenY：触摸目标在屏幕中的Y坐标
                - target：触摸到DOM节点目标
    -   手势事件
        - gesturestart：当一个手指已经按在屏幕上了而另一个手指又触摸屏幕时触发
        - gesturechange：当触摸屏幕的任何一个手指的位置发生变化时触发
        - gestureend：当任何一个手指从屏幕上移开时触发

-   事件委托
    - 对于"事件处理程序过多"的问题解决方案就是事件委托。事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件
-   移除事件管理程序
    - 把事件处理程序赋值为null，即可移除

-   模拟事件
       - DOM中的事件模拟：可以在document对象上使用createEvent()方法创建event对象。这个方法接收一个参数，即要模拟事件类型的字符串。UIEvents一般化的UI事件，MouseEvents一般化的鼠标事件，MutationEvents一般化的DOM变动事件，HTMLEvent一般化的HTML事件
        - 模拟鼠标事件
            -  createEvent("MouseEvents"),返回的对象有一个initMouseEvent()方法，接收以下15个参数
            -  type（字符串）：表示要触发的事件类型
            -  bubbles（布尔值）：表示事件是否可以应该冒泡，为精确地模拟鼠标事件，应该设置为true
            -  cancelable（布尔值）：表示事件是否可以取消，为精确地模拟鼠标事件，应该设置为true
            -  view：这个参数总应该设置为document.defaultView
            -  detail(整数)：与事件有关的详细信息。这个值一般是事件处理程序用，通常都设置为0
            -  screenX：事件相对于屏幕的X坐标
            -  screenY：事件相对于屏幕的Y坐标
            -  clientX：事件相对于视口的X坐标
            -  clientY：事件相对于视口的Y坐标
            -  ctrlKey：表示是否按下了ctrl键
            -  altKey：表示是否按下了alt键
            -  shiftKey：表示是否按下了shift键
            -  metaKey：表示是否按下了Meta键
            -  button：表示按下了哪一个鼠标键，默认为0
            -  relatedTarget（对象）：表示与事件相关的对象。这个参数只在模拟 mouseover 或 mouseout
时使用。
        - 模拟键盘事件
            - 调用 createEvent()并传入"KeyboardEvent"就可以创建一个键盘事件。返回的
事件对象会包含一个 initKeyEvent()方法，这个方法接收下列参数。
            -  type（字符串）：表示要触发的事件类型
            -  bubbles（布尔值）：表示事件是否可以应该冒泡，为精确地模拟鼠标事件，应该设置为true
            -  cancelable（布尔值）：表示事件是否可以取消，为精确地模拟鼠标事件，应该设置为true
            -  view：这个参数总应该设置为document.defaultView
            -  key：表示按下的键码
            -  location：表示按下了哪里的键：0 表示默认的主键盘，1 表示左，2 表示右，3 表示
数字键盘，4 表示移动设备（即虚拟键盘），5 表示手柄。
            - modifiers（字符串）：空格分隔的修改键列表，如"Shift"。
            - repeat（整数）：在一行中按了这个键多少次。