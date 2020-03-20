### 选择符API
- querySelector()方法
    ```
    querySelector()方法接收一个css选择符,返回鱼该模式匹配的第一个元素，如果没有找到匹配的元素就返回null。
    通过Document类型调用querySelector()方法，会在整个文档范围内查找匹配
    通过Element类型调用querySelector()方法，会在整个后代范围内查找匹配
    ```
- querySelectAll()方法
    ```
    与quertSelector()方法使用方式一样
    但是会返回匹配的一个NodeList对象，性能没有querySelector()好
    ```
- matchesSelector()方法
    ```
    如果调用元素与该css选择符匹配就会返回true，否则返回false，有兼容性问题
    ```

### 元素遍历
-   childElementCount：返回子元素（不包括文本节点和注释）的个数
-   firstElementChild：指向第一个元素：firstChild的元素版
-   lastElemntChild：指向最后一个子元素：lastChild的元素版
-   previousElmentSibling：指向前一个同辈元素：preViousSibling的元素版
-   nextElmentSibling：指向后一个同辈元素：nextSibling的元素版

### HTML5
-   getElementsByClassName()方法：可接收一个或多个类名，顺序不分前后
-   classList属性：获取类名集合
    -  add(value)：将给定的字符串值添加到列表中，如果值已存在就不再天机了
    -  contains(value)：表示列表中时候存在给定的值，如果存在则返回true，否则返回false
    -  remove(value)：表示列表中存在给定的值的时候，删除给定的字符
    -  toggle(value)：如果列表中有存在给定的值，删除它，如果列表中没有存在给定的值就添加该值
-   焦点管理
    -  document.activeElement：获得当前文档获得焦点的元素
    -  document.hasFocus()：判断当前文档是否获取了焦点
-   HTMLDocument
    - readyState：如果为loading，正在加载文档，如果为complete，已经加载完文档
-   自定义数据属性
    - data-自定义属性：可通过dataset属性访问
-   插入标记
    - innerHtml：不支持innerHtml的有
    
    ```
    <col>,<colgroup>,<frameset>,<head>,<html>,<style>,<table>,<tbody>,<thead>,<tfoot>,<tr>
    ```

    - outerHtml：内容取代当前的元素
    - insertAdjacentHTML()：接收两个参数，第二个参数为内容,第一个参数为插入的位置，为以下四个值:
        - beforebegin：在当前元素之前插入一个紧邻的同辈元素
        - afterbegin：在当前元素之下插入一个新的子元素或者在第一个子元素之前再插入一个新元素
        - beforeend:在当前元素之下插入一个新元素或者再最后一个子元素之后再插入一个新元素
        - afterend：在当前元素之后插入一个紧邻的同辈元素
-   scrollIntoView()方法：滚动页面，让元素可见，可传入参数true或false，传入true或不传，让元素顶部与页面顶部对     齐，传入false，尽可能全部展示在视图中，或者底部与视图底部对齐
-   专有扩展
    - 文档模式
    - children属性
    - contains()方法：判断某个节点是不是另一个节点的后代，返回true或者false
-   插入文本
    - innnerText：操作元素中包含的所有文本内容
    - outerText：与innerText一样，不过会替换整个元素

-   滚动
    - scrollIntoViewfNeeded(true)：让在试图不可见的元素滚动到视图中是元素可见，如果传入参数为true，则尽量滚到视图中部
    - scollByLines(lineCount)：将元素的内容滚动指定的行高，lineCount可以是正值，也可以是负值
    - scollBypages(pageCount)：将元素滚动指定的页面高度