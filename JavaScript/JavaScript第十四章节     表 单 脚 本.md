### 表单的基础知识
- acceptCharset：服务器能够处理的字符集；等价于 HTML 中的 accept-charset 特性。
- action：接受请求的 URL；等价于 HTML 中的 action 特性。
- elements：表单中所有控件的集合（HTMLCollection）。
- enctype：请求的编码类型；等价于 HTML 中的 enctype 特性。
- length：表单中控件的数量。
- method：要发送的 HTTP 请求类型，通常是"get"或"post"；等价于 HTML 的 method 特性。
- name：表单的名称；等价于 HTML 的 name 特性。
- reset()：将所有表单域重置为默认值。
- submit()：提交表单。
- target：用于发送请求和接收响应的窗口名称；等价于 HTML 的 target 特性。
### 提交表单
- 把type设置为sumbit即可
- 图像按钮把input的type设置为image
- form.submit()
### 重置表单
- 同提交表单，把type设置为reset即可
- form.reset()
### 共有的表单字段属性
- disabled：布尔值，表示当前字段是否被禁用。
- form：指向当前字段所属表单的指针；只读。
- name：当前字段的名称。
- readOnly：布尔值，表示当前字段是否只读。
- tabIndex：表示当前字段的切换（tab）序号。
- type：当前字段的类型，如"checkbox"、"radio"，等等。
- value：当前字段将被提交给服务器的值。对文件字段来说，这个属性是只读的，包含着文件
在计算机中的路径。
### 共有表单的方法
- focus()：激活表单
- blur()：失去焦点
### 共有表单字段事件
-  blur：当前字段市区焦点时触发
-  change：对于<input>和<textarea>元素，在它们失去焦点且 value 值改变时触发；对于
<select>元素，在其选项改变时触发。
- focus：当前字段获得焦点时触发。

### 选择文本
- input和textarea的select()
- setSelectionRange()：这个方法接收两个参数：要选择的第一个字符的索引和要选择的最后一个字符之后的字符的索引
（类似于 substring()方法的两个参数）。

### 过滤输入
-  屏蔽字符
    -  keypress：可以通过此事件实现屏蔽字符
        
  ```
    EventUtil.addHandler(textbox, "keypress", function(event){
     event = EventUtil.getEvent(event);
     EventUtil.preventDefault(event);
    }); 
  ```

### 操作剪贴板
- beforecopy：在发生复制操作前触发。
- copy：在发生复制操作时触发。
- beforecut：在发生剪切操作前触发。
- cut：在发生剪切操作时触发。
- beforepaste：在发生粘贴操作前触发。
- paste：在发生粘贴操作时触发。

### HTML5 约束验证API 
- 必填字段：required
- 其他输入类型，对type赋值
- 数值范围：step，max，min
- 输入模式：pattern，匹配正则
- 检测有效性：checkValidity()
- 禁用验证：novalidate

### 选择框脚本
> 选择框是通过<select>和<option>元素创建的。为了方便与这个控件交互，除了所有表单字段共
有的属性和方法外，HTMLSelectElement 类型还提供了下列属性和方法。

- add(newOption,relOption)：向控件中插入新<option>元素，其位置在相关项（relOption）
之前。
- multiple：布尔值，表示是否允许多项选择；等价于 HTML 中的 multiple 特性。
- options：控件中所有<option>元素的 HTMLCollection。
- remove(index)：移除给定位置的选项。
- selectedIndex：基于 0 的选中项的索引，如果没有选中项，则值为-1。对于支持多选的控件，
只保存选中项中第一项的索引。
- size：选择框中可见的行数；等价于 HTML 中的 size 特性。

> 在 DOM 中，每个<option>元素都有一个 HTMLOptionElement 对象表示。为便于访问数据，
HTMLOptionElement 对象添加了下列属性：

- index：当前选项在 options 集合中的索引。
- label：当前选项的标签；等价于 HTML 中的 label 特性。
- selected：布尔值，表示当前选项是否被选中。将这个属性设置为 true 可以选中当前选项。
- text：选项的文本。
- value：选项的值（等价于 HTML 中的 value 特性）。

> 选择选项:对于只允许选择一项的选择框，访问选中项的最简单方式，就是使用选择框的 selectedIndex 属
性

> 添加选项:可以使用 JavaScript 动态创建选项，并将它们添加到选择框中。
> 移除选项：
- 首先，可以使用 DOM 的 removeChild()方法
- 可以使用选择框的 remove()方法
> 移动和重排选项
- appendChild()
- insertBefore()

### 表单序列化：浏览器是怎样将数据发送给服务器的。
-  对表单字段的名称和值进行 URL 编码，使用和号（&）分隔
-  不发送禁用的表单字段。
-  只发送勾选的复选框和单选按钮。
-  不发送 type 为"reset"和"button"的按钮
-  多选选择框中的每个选中的值单独一个条目。
-  在单击提交按钮提交表单的情况下，也会发送提交按钮；否则，不发送提交按钮。也包括 type
为"image"的<input>元素。
-   <select>元素的值，就是选中的<option>元素的 value 特性的值。如果<option>元素没有
value 特性，则是<option>元素的文本值

### 富文本编辑
- 是在页面中嵌入一个包含空 HTML 页面的 iframe。通过设置 designMode 属性，这个空白
的 HTML 页面可以被编辑，而编辑对象则是该页面<body>元素的 HTML 代码。designMode 属性有两
个可能的值："off"（默认值）和"on"。在设置为"on"时，整个文档都会变得可以编辑（显示插入符
号），然后就可以像使用字处理软件一样，通过键盘将文本内容加粗、变成斜体，等等。
- 使用contenteditable属性
> 操作富文本：与富文本编辑器交互的主要方式，就是使用 document.execCommand()。这个方法可以对文档执
> 行预定义的命令，而且可以应用大多数格式。可以为 document.execCommand()方法传递 3 个参数：
> 要执行的命令名称、表示浏览器是否应该为当前命令提供用户界面的一个布尔值和执行命令必须的一个
> 值（如果不需要值，则传递 null）。为了确保跨浏览器的兼容性，第二个参数应该始终设置为 false，
> 因为 Firefox 会在该参数为 true 时抛出错误。

> 不同浏览器支持的预定义命令也不一样。下表列出了那些被支持最多的命令。
![微信截图_20191104222219.png][1]


  [1]: http://www.zzvlm.com/usr/uploads/2019/11/2959249406.png
 
- queryCommandEnabled()，可以用它来检
测是否可以针对当前选择的文本，或者当前插入字符所在位置执行某个命令。这个方法接收一个参数，即要
检测的命令。如果当前编辑区域允许执行传入的命令，这个方法返回 true，否则返回 false。
- queryCommandValue()，用于取得执行命令时传入的值（即前面例子中传给
document.execCommand()的第三个参数）。例如，在对一段文本应用"fontsize"命令时如果传入了
7，那么下面的代码就会返回"7"

### 富文本选区
> 在富文本编辑器中，使用框架（iframe）的 getSelection()方法，可以确定实际选择的文本。
这个方法是 window 对象和 document 对象的属性，调用它会返回一个表示当前选择文本的 Selection
对象。每个 Selection 对象都有下列属性。
- anchorNode：选区起点所在的节点。
- anchorOffset：在到达选区起点位置之前跳过的 anchorNode 中的字符数量
- focusNode：选区终点所在的节点。
- focusOffset：focusNode 中包含在选区之内的字符数量。
- isCollapsed：布尔值，表示选区的起点和终点是否重合
- rangeCount：选区中包含的 DOM 范围的数量。
- addRange(range)：将指定的 DOM 范围添加到选区中。
- collapse(node, offset)：将选区折叠到指定节点中的相应的文本偏移位置。
- collapseToEnd()：将选区折叠到终点位置。
- collapseToStart()：将选区折叠到起点位置。
- containsNode(node)：确定指定的节点是否包含在选区中。
- deleteFromDocument()：从文档中删除选区中的文本，与document.execCommand("delete",
false, null)命令的结果相同。
- extend(node, offset)：通过将 focusNode 和 focusOffset 移动到指定的值来扩展选区。
- getRangeAt(index)：返回索引对应的选区中的 DOM 范围。
- removeAllRanges()：从选区中移除所有 DOM 范围。实际上，这样会移除选区，因为选区中
至少要有一个范围。
- reomveRange(range)：从选区中移除指定的 DOM 范围。
- selectAllChildren(node)：清除选区并选择指定节点的所有子节点。
- toString()：返回选区所包含的文本内容。