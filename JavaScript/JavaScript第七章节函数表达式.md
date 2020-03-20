- 定义函数的两钟方式
    - 函数声明 
        ```
            //函数提升
            function name(){
                
            }
        ```
    - 函数表达式
        ```
            // 方式一
            var funtionname=function（）{   //匿名函数，没有函数名，也叫拉姆达函数
                
            }
            //  
        ```
        
- 递归
  - 递归函数时在一个函数通过名字调用自己的情况
 
        ```
        function factorial(num){
            if(num<=1){
                return 1
            }else{
                return num*factorial(num-1)
            }
        }
        //  argumente.callee()指向正在执行的函数的指针
         function factorial(num){
            if(num<=1){
                return 1
            }else{
                return argumente.callee(num-1) 
            }
        }
        ```
-  闭包
    - 闭包是指有权访问另一个函数作用域中的变量的函数

- 模仿块级作用域
    ```
    function(){
        
    }
    // 给函数声明加一个（）即变成表达式，然后立即调用
    (function (){
        // 这是块级作用域
    })()
    ```
- 私有变量
    ```
    function MyObject(){
        //私有变量和私有函数
        var privateVariable=10;
        
        function privateFunction(){
            return false
        }
        
        // 特权方法
        this.publicMethod=function (){
            privateVariable++
            return privateFunction()
        }
    }
    // 有权访问私有变量和私有方法的公有方法称为特权方法
    ```
- 静态私有变量
    ```
    (function(){
       //私有变量和私有函数
        var privateVariable=10;
        
        function privateFunction(){
            return false
        }
        // 构造函数
        MyObject=function(){
            
        }
        // 公有/特权方法
        MyObject.prototype.publicMethond=function(){
             privateVariable++
            return privateFunction()
        }
        
    })()
    ```
- 模块模式
    - 模块模式是通过为单例添加私有变量和特权方法
        ```
        var singleton=function(){
            name:value,
            methon:function(){
                // 这里是方法的代码
            }
        }
        
        // 模块模式
        var singleton=function(){
             //私有变量和私有函数
                var privateVariable=10;
                
                function privateFunction(){
                    return false
                }
            return {
                publicProperty:true,
                publicMethond:function(){
                     privateVariable++
                    return privateFunction()
                }
            }
        }
        ```