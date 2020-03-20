#### 节点层次
 - 文档元素html
 - Node类型
    - 每个节点都有nodeType,可根据nodeType来判断系欸DNA类型，详见高程248页
    - 节点关系，每个节点都有childNodes属性，保存着NodeList类数组对象
    - 操作节点:
        - appendChild()：在末尾添加节点
        - insertBefore()：接收两个参数，第一个为需要插入的节点，第二个为插入节点的位置
        - replaceChild()：替换节点，要替换的节点，要被替换的节点
        - removeChild()：移除节点
        - cloneNode()：接收一个布尔值参数，是否对克隆的对象进行深复制
        - normallize()：处理空文本节点
- Document类型
    - nodeType:9
    - nodeName:"#document"
    - nodeValue:null,
    - parentNode:null,
    - ownerDocument:null,
    - document.title：获取文档标题
    - document.URL：获取文档url
    - document.domain：获取文档主域名
    - document.getElementById()：通过id查找节点
    - document.getElementByTagName()：通过元素标签名查找元素
    - HtmlDocument.getElementByName()：通过元素的name特性查找元素
    - document.anchors：获取文档中所有带name的a标签
    - document.forms：获取文档中所有的form元素
    - document.images：获取文档中所有的img元素
    - document.link：获取文档中所有带href特性的a标签

- 文档写入
    - document.write()：输出文档
    - document.writeln()：输出文档
    - document.open()：打开网页输出流
    - document.close()：关闭网页输出流
- Element类型
    - nodeType：1
    - nodeName：元素的标签名
    - nodeValue：null，
    - parentNode：Document或者element
    - 子节点可能是：element,text,comment,processinginstrunction,CDATASection,EntityReference

- HTML元素（标准特性）
    - id
    - title
    - lang
    - dir
    - className
    - 取得特性：getAttribute()
    - 设置特性: setAttribute(name,value)

- attributes属性
    - getNamedItem(name)：返回nodename属性等于name的节点
    - removeNamedItem(name)：从列表中移除nodeName属性等于name的节点
    - setNamedItem(node)：向列表中添加节点，以节点的nodeName属性为索引
    - item(pos)：返回位于数字pos位置处的节点

- 创建节点
    - document.createElement()：创建新元素

- Text类型
    - nodeType：1
    - nodeName:'#text'
    - nodeValue：节点所包含的文本
    - parentNode：Element
    - appendData(text)：将text添加到节点末尾
    - deleteData(offset,count)：从offset位置开始删除count个字符
    - insertData(offset,text)：从offset位置插入text字符
    - replaceData(offset,count,text)：用text替换从offset开始到offset+count为止的处的文本
    - splitText(offset)：从offset指定的位置将当前文本节点分成两个文本节点
    - substringData(offset,count)：提取从offset指定的位置开始到offset+count为止的字符串

- 创建文本节点
    - document.createTextNode()：创建文本节点

- comment类型(注释节点)
    - nodeType：8
    - nodeName：#comment
    - nodeValue：注释的内容
    - parentNode:Document或者Element

- CDATASection类型(只争对于XML)
    - nodeType：4
    - nodeName：#cdata-section
    - nodeValue：CDATA区域的内容

- DocumentType
    - nodeType:10
    - nodeName:doctype的名称
    - nodeValue：null
    - parentNode：Document

-  DocumentDragment
    - nodeType:11
    - nodeName：#document-fragment
    - nodeValue：null
    - parentNode：null
    - 没有对应标记

-  Attr类型
    - nodeType：2
    - nodeName：特性的名字
    - nodeValue：特性的值
    - parentNode：null
    - atter有三个值：name,value和用来区分是否是1默认的特性的specified

- DOM操作技术
    - 动态脚本
        - 创建标签法
        - <script></script>法
    - 动态样式
        - 创建标签法
        - <style></style>法

- 操作表格
    - caption：保存着对<caption>元素(如果有)的指针
    - tBodies：是一个<tbody>的HTMLCollection
    - tFoot：保存着对<tfoot>元素(如果有)的指针
    - tHead：保存着对<thead>元素（如果有）的指针
    - rows：是一个表格中所有行的HTMLColletion
    - createTHead：创建<thead>元素，将其放在表格中，返回引用
    - createTFoot：创建<tfoot>元素，将其放在表格中，返回引用
    - createCaption：创建<caption>元素，将其放在表格中，返回引用
    - deleteThead：删除<thead>元素
    - deleteTfoot：删除<tfoot>元素
    - deleteRow(pos)：删除指定位置的行
    - deleteCapition()：删除<caption>元素
    - insertRow(pos)：向指定位置插入一行
    - deleteCell(pos)：删除指定位置的单元格
    - insertCell(pos)：向指定位置插入一行

    