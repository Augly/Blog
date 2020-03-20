- Windows对象
    - 全局作用域
        - 全局作用域中声明的变量，函数和方法都会变成windows的属性和方法
    - 窗口关系及框架
        - 每个框架都有自己的windows对象
        - 可通过windows.frames进行访问
        - frames可用frameset包裹
    - 窗口位置
        - screenLeft(screenX)：窗口相对于屏幕左边
        - screenTop(screenY)：窗口相对于屏幕上边
        - moveTo(x,y)：x和y表示新位置的坐标值
        - moveBy(x,y)：x和y表示水平和垂直方向上的像素数
    - 窗口大小
        - outerWidth和outerHeight：返回浏览器窗口本身的尺寸
        - innerWidth和innerHeight：返回浏览器除去边框的视图容器大小
        - clientWidth和clientHeight：返回浏览器视口尺寸
        - resizeTo()：接收两个参数浏览器窗口的新宽度和新高度
        - resizeBy()：接收两个参数，新窗口与原窗口的宽度差和高度差
    - 导航和打开窗口
        - window.open()：
            - 第一个参数是要加载的url
            - 第二个参数为窗口目标，也可以为 ==_self==,==_parent==, ==_top== 或者 ==_blank==
            - 第三个参数为特性字符串及设置新窗口的特性详见高程第200页
            - 第四个参数为是否取代原网页在历史记录中的位置
        - window.close()：关闭新打开的窗口
    - 弹出窗口屏蔽程序
        ```
        var blocked=false
        try{
            var wroxWin=window.open('http://www.baidu.com',_blank)
            if(wroxWin==null){
                blocked=true
            }
        }catch(ex){
            blocked=truee
        }
        
        if(blocked){
            alert("the popup was blocked!")
        }
        ```
     - 间歇调用和超时调用
        - setTimeout(function(){},time)，time告诉程序，time后把setTimeout放到js队列里去
        - 清除setTime：clearTimeout()  
        - 间歇调用：setInterval(function(){},time)
        - 清除间歇调用：clearInterval()
    - 系统对话框
        - alert()：警告提示框
        - confirm()：二次确认弹框
            ```
            if(confirm('Are you ok?')){
                // true
            }else{
                // false
            }
            ```
        - prompt()：除了有confirm的功能外，还会提供一个文本输入框，prompt()接收两个参数，第一个是要显示给用户的文本提示和文本输入框的默认值
             ```
            if(prompt('Are you ok?','')){
                // true
            }else{
                // false
            }
            ```
            
        - window.print()：显示打印对话框
        - window.find()：显示查找对话框
    
    - location对象
        - 查询字符串参数：location.search,可查询到url中？到末尾的字符串
        - 位置操作
            - location.assign(newUrl)
            - location.href：修改url
            - location.hash: 修改url后面的#
            - location.search：给url传参
            - location.hostname：修改host主域名
            - location.pathname：修改文件资源名
            - location.port：修改访问端口
            - location.replace(url)：使用户不能回到前一个页面，断开历史记录
            - location.reload()：重新加载页面，接收一个Boolean值，默认为false，如果为true，则强制重新从服务器加载新资源
    
    - navigator对象
        - 详细请看高程210页表格
        - 检测插件
            navigator.plugins。可以得到一个数组，每项都包含name,description,filename,length
    - history对象
        - history.go(-1)：//后退一页
        - history.go(1): //前进一页
        - history.go(2): //前进两页
        - history.go(string)：跳到最近的host为string的网址
        - history.back()：后退
        - history.forward()：前进
        