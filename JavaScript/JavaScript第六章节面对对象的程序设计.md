- 理解对象
    - 属性类型
        - 数据属性
            - Configurable：表示能否通过delete删除属性从而定义属性
            - Enumberable：表示是否能通过for-in循环返回属性
            - Writeable：表示能否修改属性的值
            - Value：包含这个属性的数据值
            - 要修改属性的默认的特性，需要使用Object.defineProperty(),该方法接收三个参数：属性所在的对象，属性的名字和一个描述符对象，其中描述符对象的属性必须是：configurable，enumberable,writable和value，设置一个或多个值，即可修改
        - 访问器属性
            - 访问器属性有四个特征
                - configurable
                - enumberable
                - get:在读取属性时调用的函数，默认为undefined
                - set:在设置属性时调用的函数，默认为undefined
            - 访问器属性的定义必须通过Object.defineProperty()来定义
            - 只指定get，即设置为对象不可写
            - 只指定set，即设置为对象不可读
        - 定义多个属性
            - Object.defineProperties()可同时定义多个属性
        - 读取属性特性
            - Object.getOwnPropertyDescriptor()方法，接收两个参数，第一个参数为需要读取的属性所在的对象，第二个参数为需要读取的属性特性的属性名

- 创建对象
    - 工厂模式
        
        ```
        function obj(name,age,job){
            var o=new Object()
            o.name=name
            o.age=age
            o.job=job
            return 0
        }
        var person=obj('仇益阳'，24，'web前端工程师')
        ```
    - 构造函数模式
        
        ```
        function Obj(name,age,job){
            this.name=name
            this.age=age
            this.job=job
        }
        var person=new Obj('仇益阳'，24，'web前端工程师')
        构造函数函数名始终以大写字母开头，非构造函数以小写字母开头
        ```
- 原型模式
    - prototype：这个属性是一个指针，指向一个原型对象
    - prototype.isPrototypeOf()可判断判断此构造函数是否继承于这个原型
    - hasOwnProperty('name') ：对象实例本身是否有'name'这个属性
    - 原型与in操作符: 
            
    ```
        单独使用：判断对象能否访问该属性，能访问返回true，无论是在原型中还是在实例中，否则反之
        在fon-in循环中使用：遍历循环
        Object。keys(Obj)：获取对象上可遍历的属性，只返回当前实例有的属性
    ```
    - 更简单的原型语法
    - 原型对象的问题
        - 默认情况下所有实例都将取得相同的属性值

- 继承
    - 原型链继承
        - 基本方式
        ```
        function SuperType(){
            this.property=true
        }
        SuperType.prototype.getSuperValue=function(){
            return this.property
        }
        function SubType(){
            this.subproperty=false
        }
        // 继承了SuperType
        SubType.prototype=new SuperType()
        SubType.prototype.getSubValue=function (){
            return this.subproperty
        }
        var instance=new Subtype()
        alert(instance.getSuperValue())
        ```
        - 给原型添加方法必须要放在替换原型之后
        - 用对象字面量创建原型是会重写原型链
        - 原型链的问题
            - 引用类型的的原型属性会被所有实例继承
            - 在创建子类型的实例时，不能向超类行的构造函数中传递参数
    - 借用构造函数（伪造类型或者经典继承）
        - 通过apply()和call()继承
         ```
        function SuperType(){
            this.property=true
        }
        function SubType(){
           SuperType.call(this)
        }
        var instance=new Subtype()
        alert(instance.property)
        ```
        - 可传递参数
        ```
        function SuperType(name){
            this.name=name
            this.age=23
        }
        function SubType(){
           SuperType.call(this,'仇益阳')
        }
        var instance=new Subtype()
        alert(instance.name)
        ```
        -  借用构造函数的问题
            - 方法都在构造函数里，没法复用
    - 组合继承
        - 原型继承和构造函数相结合
        - 原型继承通用的属性和方法
        - 构造函数继承差异化属性和方法
        
    - 原型式继承
        - 使用Object.create()
    - 寄生式继承
    - 寄生组合式继承
     