### WebGL
> WebGL 是针对 Canvas 的 3D 上下文。与其他 Web 技术不同，WebGL 并不是 W3C 制定的标准，而
是由 Khronos Group 制定的。其官方网站是这样介绍的：“Khronos Group 是一个非盈利的由会员资助的
协会，专注于为并行计算以及各种平台和设备上的图形及动态媒体制定无版税的开放标准。” Khronos
Group 也设计了其他图形处理 API，比如 OpenGL ES 2.0。浏览器中使用的 WebGL 就是基于 OpenGL ES
2.0 制定的。

[WebGL学习地址](www.learningwebgl.com),
[OpenGL](www.opengl.org)

#### 类型化数组
> WebGL 涉及的复杂计算需要提前知道数值的精度，而标准的 JavaScript 数值无法满足需要。为此，WebGL 引入了一个概念，叫类型化数组（typed arrays）。类型化数组也是数组，只不过其元素被设置为特定类型的值。
> 类型化数组的核心就是一个名为 ArrayBuffer 的类型。每个 ArrayBuffer 对象表示的只是内存中指定的字节数，但不会指定这些字节用于保存什么类型的数据。通过 ArrayBuffer 所能做的，就是为了将来使用而分配一定数量的字节。

##### 视图
> 使用 ArrayBuffer（数组缓冲器类型）的一种特别的方式就是用它来创建数组缓冲器视图。其中，最常见的视图是 DataView，通过它可以选择 ArrayBuffer中一小段字节。为此，可以在创建 DataView实例的时候传入一个 ArrayBuffer、一个可选的字节偏移量（从该字节开始选择）和一个可选的要选
择的字节数。

示例:
```
//基于整个缓冲器创建一个新视图
var view = new DataView(buffer);
//创建一个开始于字节 9 的新视图
var view = new DataView(buffer, 9);
//创建一个从字节 9 开始到字节 18 的新视图
var view = new DataView(buffer, 9, 10); 
```

##### 类型化视图
> 类型化视图一般也被称为类型化数组，因为它们除了元素必须是某种特定的数据类型外，与常规的
数组无异。类型化视图也分几种，而且它们都继承了 DataView。
    
 - Int8Array：表示 8 位二补整数。
 - Uint8Array：表示 8 位无符号整数。
 - Int16Array：表示 16 位二补整数。
 - Uint16Array：表示 16 位无符号整数。
 - Int32Array：表示 32 位二补整数。
 - Uint32Array：表示 32 位无符号整数。
 - Float32Array：表示 32 位 IEEE 浮点值。
 - Float64Array：表示 64 位 IEEE 浮点值。

#### WebGL上下文
> 目前，在支持的浏览器中，WebGL 的名字叫"experimental-webgl"，这是因为 WebGL 规范仍然未制定完成。制定完成后，这个上下文的名字就会变成简单的"webgl"。如果浏览器不支持 WebGL，那么取得该上下文时会返回 null。

示例:
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 var gl = drawing.getContext("experimental-webgl");
 if (gl){
 //使用 WebGL
 }
} 
```
> 通过给 getContext()传递第二个参数，可以为 WebGL 上下文设置一些选项。

- alpha：值为 true，表示为上下文创建一个 Alpha 通道缓冲区；默认值为 true。
- depth：值为 true，表示可以使用 16 位深缓冲区；默认值为 true。
- stencil：值为 true，表示可以使用 8 位模板缓冲区；默认值为 false。
- antialias：值为 true，表示将使用默认机制执行抗锯齿操作；默认值为 true。
- premultipliedAlpha：值为 true，表示绘图缓冲区有预乘 Alpha 值；默认值为 true。
- preserveDrawingBuffer：值为 true，表示在绘图完成后保留绘图缓冲区；默认值为 false。

示例:
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 try {
 gl = drawing.getContext("experimental-webgl");
 } catch (ex) {
 //什么也不做
 }
 if (gl){
 //使用 WebGL
 } else {
 alert("WebGL context could not be created.");
 }
} 
```
#### 常量
> 如果你熟悉 OpenGL，那肯定会对各种操作中使用非常多的常量印象深刻。这些常量在 OpenGL 中
都带前缀 GL_。 在 WebGL 中，保存在上下文对象中的这些常量都没有 GL_前缀。比如说，
GL_COLOR_BUFFER_BIT 常量在 WebGL 上下文中就是 gl.COLOR_BUFFER_BIT。WebGL 以这种方式支
持大多数 OpenGL 常量（有一部分常量是不支持的）。

#### 方法命名
> OpenGL（以及 WebGL）中的很多方法都试图通过名字传达有关数据类型的信息。如果某方法可以
接收不同类型及不同数量的参数，看方法名的后缀就可以知道。方法名的后缀会包含参数个数（1 到 4）
和接收的数据类型（f 表示浮点数，i 表示整数）。例如，gl.uniform4f()意味着要接收 4 个浮点数，
而 gl.uniform3i()则表示要接收 3 个整数。
也有很多方法接收数组参数而非一个个单独的参数。这样的方法其名字中会包含字母 v（即 vector，
矢量）。因此，gl.uniform3iv()可以接收一个包含 3 个值的整数数组。请

#### 准备绘图
> 在实际操作 WebGL 上下文之前，一般都要使用某种实色清除<canvas>，为绘图做好准备。为此，
首先必须使用 clearColor()方法来指定要使用的颜色值，该方法接收 4 个参数：红、绿、蓝和透明度。
每个参数必须是一个 0 到 1 之间的数值，表示每种分量在最终颜色中的强度。

```
gl.clearColor(0,0,0,1); //black
gl.clear(gl.COLOR_BUFFER_BIT);

以上代码把清理颜色缓冲区的值设置为黑色，然后调用了 clear()方法，这个方法与 OpenGL 中的
glClear()等价。传入的参数 gl.COLOR_BUFFER_BIT 告诉 WebGL 使用之前定义的颜色来填充相应区
域。一般来说，都要先清理缓冲区，然后再执行其他绘图操作。

```
#### 视口与坐标
> 开始绘图之前，通常要先定义 WebGL 的视口（viewport）。默认情况下，视口可以使用整个<canvas>
区域。要改变视口大小，可以调用 viewport()方法并传入 4 个参数：（视口相对于<canvas>元素的）
x 坐标、y 坐标、宽度和高度。

####  缓冲区
> 顶点信息保存在 JavaScript 的类型化数组中，使用之前必须转换到 WebGL 的缓冲区。要创建缓冲区，可以调用 gl.createBuffer()，然后使用 gl.bindBuffer()绑定到 WebGL 上下文。这两步做完之
后，就可以用数据来填充缓冲区了。

> 调用 gl.bindBuffer()可以将 buffer 设置为上下文的当前缓冲区。此后，所有缓冲区操作都
直接在 buffer 中执行。因此，调用 gl.bufferData()时不需要明确传入 buffer 也没有问题。最后
一行代码使用 Float32Array 中的数据初始化了 buffer（一般都是用 Float32Array 来保存顶点信
息）。如果想使用 drawElements()输出缓冲区的内容，也可以传入 gl.ELEMENT_ARRAY_BUFFER。
gl.bufferData()的最后一个参数用于指定使用缓冲区的方式，取值范围是如下几个常量。

-  gl.STATIC_DRAW：数据只加载一次，在多次绘图中使用。
-  gl.STREAM_DRAW：数据只加载一次，在几次绘图中使用。
-  gl.DYNAMIC_DRAW：数据动态改变，在多次绘图中使用。
-  gl.deleteBuffer(buffer)：删除缓存，释放内存


#### 错误
> JavaScript 与 WebGL 之间的一个最大的区别在于，WebGL 操作一般不会抛出错误。为了知道是否
有错误发生，必须在调用某个可能出错的方法后，手工调用 gl.getError()方法。这个方法返回一个
表示错误类型的常量。可能的错误常量如下。

- gl.NO_ERROR：上一次操作没有发生错误（值为 0）。
- gl.INVALID_ENUM：应该给方法传入 WebGL 常量，但却传错了参数。
- gl.INVALID_VALUE：在需要无符号数的地方传入了负值。
- gl.INVALID_OPERATION：在当前状态下不能完成操作。
- gl.OUT_OF_MEMORY：没有足够的内存完成操作。
- gl.CONTEXT_LOST_WEBGL：由于外部事件（如设备断电）干扰丢失了当前 WebGL 上下文。

####  着色器
> 着色器（shader）是 OpenGL 中的另一个概念。WebGL 中有两种着色器：顶点着色器和片段（或像
素）着色器。顶点着色器用于将 3D 顶点转换为需要渲染的 2D 点。片段着色器用于准确计算要绘制的每个像素的颜色。WebGL 着色器的独特之处也是其难点在于，它们并不是用 JavaScript 写的。这些着色
器是使用 GLSL（OpenGL Shading Language，OpenGL 着色语言）写的，GLSL 是一种与 C 和 JavaScript
完全不同的语言。

#### 编写着色器
> GLSL 是一种类 C 语言，专门用于编写 OpenGL 着色器。因为 WebGL 是 OpenGL ES 2.0 的实现，所
以 OpenGL 中使用的着色器可以直接在 WebGL 中使用。这样就方便了将桌面图形应用移植到浏览器中。
每个着色器都有一个 main()方法，该方法在绘图期间会重复执行。为着色器传递数据的方式有两
种：Attribute 和 Uniform。通过 Attribute 可以向顶点着色器中传入顶点信息，通过 Uniform 可以向任何
着色器传入常量值。Attribute 和 Uniform 在 main()方法外部定义，分别使用关键字 attribute 和
uniform。

#### 编写着色器程序
> 浏览器不能理解 GLSL 程序，因此必须准备好字符串形式的 GLSL 程序，以便编译并链接到着色器
程序。为便于使用，通常是把着色器包含在页面的<script>标签内，并为该标签指定一个自定义的 type
属性。由于无法识别 type 属性值，浏览器不会解析<script>标签中的内容，但这不影响你读写其中
的代码。

示例:

```
<script type="x-webgl/x-vertex-shader" id="vertexShader">
    attribute vec2 aVertexPosition;
    void main() {
     gl_Position = vec4(aVertexPosition, 0.0, 1.0);
    }
    </script>
    <script type="x-webgl/x-fragment-shader" id="fragmentShader">
    uniform vec4 uColor;
    void main() {
     gl_FragColor = uColor;
    }
</script> 
// 然后，可以通过 text 属性提取出<script>元素的内容：
var vertexGlsl = document.getElementById("vertexShader").text,
 fragmentGlsl = document.getElementById("fragmentShader").text;
 
// 创建着色器
// 要创建着色器对象，可以调用gl.createShader()方法并传入要创建的着色器类型（gl.VERTEX_SHADER 或 gl.FRAGMENT_SHADER）。编译着色器使用的是 gl.compileShader()。

var vertexShader = gl.createShader(gl.VERTEX_SHADER);
gl.shaderSource(vertexShader, vertexGlsl);
gl.compileShader(vertexShader);
var fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);
gl.shaderSource(fragmentShader, fragmentGlsl);
gl.compileShader(fragmentShader); 

// 链接到着色器程序中。

var program = gl.createProgram();
gl.attachShader(program, vertexShader);
gl.attachShader(program, fragmentShader);
gl.linkProgram(program); 

//通知webGL使用这个程序
gl.useProgram(program); 
```

#### 为着色器传入值
> 前面定义的着色器都必须接收一个值才能工作。为了给着色器传入这个值，必须先找到要接收这个
值的变量。对于 Uniform 变量，可以使用 gl.getUniformLocation()，这个方法返回一个对象，表示
Uniform 变量在内存中的位置。然后可以基于变量的位置来赋值。

示例:
```
var uColor = gl.getUniformLocation(program, "uColor");
gl.uniform4fv(uColor, [0, 0, 0, 1]); 
//第一行代码从 program 中找到 Uniform 变量 uColor，返回了它在内存中的位置。第二行代码使用gl.uniform4fv()给 uColor 赋值。
```

#### 调试着色器和程序
>  WebGL 中的其他操作一样，着色器操作也可能会失败，而且也是静默失败。如果你想知道着色
器或程序执行中是否发生了错误，必须亲自询问 WebGL 上下文。

```
if (!gl.getShaderParameter(vertexShader, gl.COMPILE_STATUS)){
 alert(gl.getShaderInfoLog(vertexShader));
} 
```

#### 绘图
> WebGL 只能绘制三种形状：点、线和三角。其他所有形状都是由这三种基本形状合成之后，再绘
制到三维空间中的。执行绘图操作要调用 gl.drawArrays()或 gl.drawElements()方法，前者用于
数组缓冲区，后者用于元素数组缓冲区。

> gl.drawArrays()或 gl.drawElements()的第一个参数都是一个常量，表示要绘制的形状。可
取值的常量范围包括以下这些。

- gl.POINTS：将每个顶点当成一个点来绘制。
- gl.LINES：将数组当成一系列顶点，在这些顶点间画线。每个顶点既是起点也是终点，因此数
组中必须包含偶数个顶点才能完成绘制。
- gl.LINE_LOOP：将数组当成一系列顶点，在这些顶点间画线。线条从第一个顶点到第二个顶点，
再从第二个顶点到第三个顶点，依此类推，直至最后一个顶点。然后再从最后一个顶点到第一
个顶点画一条线。结果就是一个形状的轮廓。
- gl.LINE_STRIP：除了不画最后一个顶点与第一个顶点之间的线之外，其他与 gl.LINE_LOOP
相同。
- gl.TRIANGLES：将数组当成一系列顶点，在这些顶点间绘制三角形。除非明确指定，每个三角
形都单独绘制，不与其他三角形共享顶点。
- gl.TRIANGLES_STRIP：除了将前三个顶点之后的顶点当作第三个顶点与前两个顶点共同构成
一个新三角形外，其他都与 gl.TRIANGLES 相同。例如，如果数组中包含 A、B、C、D 四个顶
点，则第一个三角形连接 ABC，而第二个三角形连接 BCD。
- gl. TRIANGLES_FAN：除了将前三个顶点之后的顶点当作第三个顶点与前一个顶点及第一个顶
点共同构成一个新三角形外，其他都与 gl.TRIANGLES 相同。例如，如果数组中包含 A、B、C、
D 四个顶点，则第一个三角形连接 ABC，而第二个三角形连接 ACD。

#### 纹理
> WebGL 的纹理可以使用 DOM 中的图像。要创建一个新纹理，可以调用 gl.createTexture()，
然后再将一幅图像绑定到该纹理。如果图像尚未加载到内存中，可能需要创建一个 Image 对象的实例，
以便动态加载图像。图像加载完成之前，纹理不会初始化，因此，必须在 load 事件触发后才能设置纹
理。

示例:
```
var image = new Image(),
 texture;
image.src = "smile.gif";
image.onload = function(){
 texture = gl.createTexture();
 gl.bindTexture(gl.TEXTURE_2D, texture);
 gl.pixelStorei(gl.UNPACK_FLIP_Y_WEBGL, true);
 gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, image);
 gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.NEAREST);
 gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.NEAREST);
 //清除当前纹理
 gl.bindTexture(gl.TEXTURE_2D, null);
} 
```

#### 读取像素
> 与 2D 上下文 类似，通过 WebGL 上下文也能读取像素值。读取像素值的方法 readPixels()与
OpenGL 中的同名方法只有一点不同，即最后一个参数必须是类型化数组。像素信息是从帧缓冲区读取的，然后保存在类型化数组中。readPixels()方法的参数有：x、y、宽度、高度、图像格式、数据类
型和类型化数组。前 4 个参数指定读取哪个区域中的像素。图像格式参数几乎总是 gl.RGBA。数据类型
参数用于指定保存在类型化数组中的数据的类型，但有以下限制。

- 如果类型是 gl.UNSIGNED_BYTE，则类型化数组必须是 Uint8Array。
- 如果类型是 gl.UNSIGNED_SHORT_5_6_5、gl.UNSIGNED_SHORT_4_4_4_4 或 gl.UNSIGNED_
SHORT_5_5_5_1，则类型化数组必须是 Uint16Array。