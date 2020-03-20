### 基本用法
> 要使用<canvas>元素，必须先设置其 width 和 height 属性，指定可以绘图的区域大小。出现在
开始和结束标签中的内容是后备信息，如果浏览器不支持<canvas>元素，就会显示这些信息。

示例：

```
<canvas id="drawing" width=" 200" height="200">A drawing of something.</canvas> 
```
> 要在这块画布（canvas）上绘图，需要取得绘图上下文。而取得绘图上下文对象的引用，需要调用
getContext()方法并传入上下文的名字。传入"2d"，就可以取得 2D 上下文对象。

示例：
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){ 
    //获取2D上下文对象
    var context = drawing.getContext("2d");
    //更多代码
} 
```
> 使用 toDataURL()方法，可以导出在<canvas>元素上绘制的图像。这个方法接受一个参数，即图
像的 MIME 类型格式，而且适合用于创建图像的任何上下文。比如，要取得画布中的一幅 PNG 格式的
图像，可以使用以下代码。

```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 //取得图像的数据 URI
 var imgURI = drawing.toDataURL("image/png");
 //显示图像
 var image = document.createElement("img");
 image.src = imgURI;
 document.body.appendChild(image);
} 
```
> 默认情况下，浏览器会将图像编码为 PNG 格式（除非另行指定）。Firefox 和 Opera 也支持基于
"image/jpeg"参数的 JPEG 编码格式。由于这个方法是后来才追加的，所以支持<canvas>的浏览器也
是在较新的版本中才加入了对它的支持，比如 IE9、Firefox 3.5 和 Opera 10。

> 注意：如果绘制到画布上的图像源自不同的域，toDataURL()方法会抛出错误。

### 2D上下文
> 使用 2D 绘图上下文提供的方法，可以绘制简单的 2D 图形，比如矩形、弧线和路径。2D 上下文的
坐标开始于<canvas>元素的左上角，原点坐标是(0,0)。所有坐标值都基于这个原点计算，x 值越大表示
越靠右，y 值越大表示越靠下。默认情况下，width 和 height 表示水平和垂直两个方向上可用的像素
数目。

#### 填充和描边
> 2D 上下文的两种基本绘图操作是填充和描边。填充，就是用指定的样式（颜色、渐变或图像）填
充图形；描边，就是只在图形的边缘画线。大多数 2D 上下文操作都会细分为填充和描边两个操作，而操作的结果取决于两个属性：fillStyle 和 strokeStyle。
这两个属性的值可以是字符串、渐变对象或模式对象，而且它们的默认值都是"#000000"。如果为
它们指定表示颜色的字符串值，可以使用 CSS 中指定颜色值的任何格式，包括颜色名、十六进制码、
rgb、rgba、hsl 或 hsla。

示例：
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 var context = drawing.getContext("2d");
 context.strokeStyle = "red";
 context.fillStyle = "#0000ff";
} 

```
#### 绘制矩形
> 矩形是唯一一种可以直接在 2D 上下文中绘制的形状。与矩形有关的方法包括 fillRect()、
strokeRect()和 clearRect()。这三个方法都能接收 4 个参数：矩形的 x 坐标、矩形的 y 坐标、矩形
宽度和矩形高度。这些参数的单位都是像素。

示例:
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 var context = drawing.getContext("2d");
 /*
 * 根据 Mozilla 的文档
 * http://developer.mozilla.org/en/docs/Canvas_tutorial:Basic_usage
 */
 //绘制红色矩形
 context.fillStyle = "#ff0000";
 context.fillRect(10, 10, 50, 50);
 //绘制半透明的蓝色矩形
 context.fillStyle = "rgba(0,0,255,0.5)";
 context.fillRect(30, 30, 50, 50);
} 
```
> strokeRect()方法在画布上绘制的矩形会使用指定的颜色描边。描边颜色通
过 strokeStyle 属性指定。

示例:
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 var context = drawing.getContext("2d");
 /*
 * 根据 Mozilla 的文档
 * http://developer.mozilla.org/en/docs/Canvas_tutorial:Basic_usage
 */
 //绘制红色描边矩形
 context.strokeStyle = "#ff0000";
 context.strokeRect(10, 10, 50, 50);
 //绘制半透明的蓝色描边矩形
 context.strokeStyle = "rgba(0,0,255,0.5)";
 context.strokeRect(30, 30, 50, 50);
} 
```
> 描边线条的宽度由 lineWidth 属性控制，该属性的值可以是任意整数。另外，
通过 lineCap 属性可以控制线条末端的形状是平头、圆头还是方头（"butt"、
"round"或"square"），通过 lineJoin 属性可以控制线条相交的方式是圆交、斜
交还是斜接（"round"、"bevel"或"miter"）。

> clearRect()方法用于清除画布上的矩形区域。本质上，这个方法可以把绘制上下文中的某
一矩形区域变透明。通过绘制形状然后再清除指定区域，就可以生成有意思的效果，例如把某个形状切
掉一块。

示例:
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 var context = drawing.getContext("2d");
 /*
 * 根据 Mozilla 的文档
 * http://developer.mozilla.org/en/docs/Canvas_tutorial:Basic_usage
 */
 //绘制红色矩形
 context.fillStyle = "#ff0000";
 context.fillRect(10, 10, 50, 50);
 //绘制半透明的蓝色矩形
 context.fillStyle = "rgba(0,0,255,0.5)";
 context.fillRect(30, 30, 50, 50);
 //在两个矩形重叠的地方清除一个小矩形
 context.clearRect(40, 40, 10, 10);
} 
```

#### 绘制路径
> 2D 绘制上下文支持很多在画布上绘制路径的方法。通过路径可以创造出复
杂的形状和线条。要绘制路径，首先必须调用 beginPath()方法，表示要开始
绘制新路径。然后，再通过调用下列方法来实际地绘制路径。

- arc(x, y, radius, startAngle, endAngle, counterclockwise)：以(x,y)为圆心绘
制一条弧线，弧线半径为 radius，起始和结束角度（用弧度表示）分别为 startAngle 和
endAngle。最后一个参数表示 startAngle 和 endAngle 是否按逆时针方向计算，值为 false
表示按顺时针方向计算。
- arcTo(x1, y1, x2, y2, radius)：从上一点开始绘制一条弧线，到(x2,y2)为止，并且以
给定的半径 radius 穿过(x1,y1)。
- bezierCurveTo(c1x, c1y, c2x, c2y, x, y)：从上一点开始绘制一条曲线，到(x,y)为
止，并且以(c1x,c1y)和(c2x,c2y)为控制点。
- lineTo(x, y)：从上一点开始绘制一条直线，到(x,y)为止。
- moveTo(x, y)：将绘图游标移动到(x,y)，不画线。
- quadraticCurveTo(cx, cy, x, y)：从上一点开始绘制一条二次曲线，到(x,y)为止，并
且以(cx,cy)作为控制点。
- rect(x, y, width, height)：从点(x,y)开始绘制一个矩形，宽度和高度分别由 width 和
height 指定。这个方法绘制的是矩形路径，而不是 strokeRect()和 fillRect()所绘制的独
立的形状。
- closePath()：把起点连接起来
- fill()：在路径已经完成的情况下，填充
- stroke()：对路径进行描边

示例:
```
var drawing = document.getElementById("drawing");
//确定浏览器支持<canvas>元素
if (drawing.getContext){
 var context = drawing.getContext("2d");
 //开始路径
 context.beginPath();
 //绘制外圆
 context.arc(100, 100, 99, 0, 2 * Math.PI, false);
 //绘制内圆
 context.moveTo(194, 100);
 context.arc(100, 100, 94, 0, 2 * Math.PI, false);
 //绘制分针
 context.moveTo(100, 100);
 context.lineTo(100, 15);
 //绘制时针
 context.moveTo(100, 100);
 context.lineTo(35, 100);
 //描边路径
 context.stroke();
} 
```

#### 绘制文本
> 文本与图形总是如影随形。为此，2D 绘图上下文也提供了绘制文本的方法。绘制文本主要有两个
方法：fillText()和 strokeText()。这两个方法都可以接收 4 个参数：要绘制的文本字符串、x 坐
标、y 坐标和可选的最大像素宽度。而且，这两个方法都以下列 3 个属性为基础

- font：表示文本样式、大小及字体，用 CSS 中指定字体的格式来指定，例如"10px Arial"。
- textAlign：表示文本对齐方式。可能的值有"start"、"end"、"left"、"right"和"center"。
建议使用"start"和"end"，不要使用"left"和"right"，因为前两者的意思更稳妥，能同时
适合从左到右和从右到左显示（阅读）的语言。
- textBaseline：表示文本的基线。可能的值有"top"、"hanging"、"middle"、"alphabetic"、
"ideographic"和"bottom"。
- measureText()：这个方法接收一个参数，即要绘制的文本；返回一个 TextMetrics
对象。返回的对象目前只有一个 width 属性

#### 变换
> 通过上下文的变换，可以把处理后的图像绘制到画布上。2D 绘制上下文支持各种基本的绘制变换。
创建绘制上下文时，会以默认值初始化变换矩阵，在默认的变换矩阵下，所有处理都按描述直接绘制。
为绘制上下文应用变换，会导致使用不同的变换矩阵应用处理，从而产生不同的结果。

- rotate(angle)：围绕原点旋转图像 angle 弧度。
- scale(scaleX, scaleY)：缩放图像，在 x 方向乘以 scaleX，在 y 方向乘以 scaleY。scaleX
和 scaleY 的默认值都是 1.0。
- translate(x, y)：将坐标原点移动到(x,y)。执行这个变换之后，坐标(0,0)会变成之前由(x,y)
表示的点。
- transform(m1_1, m1_2, m2_1, m2_2, dx, dy)：直接修改变换矩阵，方式是乘以如下
矩阵。
m1_1 m1_2 dx
m2_1 m2_2 dy
0 0 1
- setTransform(m1_1, m1_2, m2_1, m2_2, dx, dy)：将变换矩阵重置为默认状态，然后
再调用 transform()。

#### 绘制图像
> 2D 绘图上下文内置了对图像的支持。如果你想把一幅图像绘制到画布上，可以使用 drawImage()
方法。根据期望的最终结果不同，调用这个方法时，可以使用三种不同的参数组合。最简单的调用方式
是传入一个 HTML <img>元素，以及绘制该图像的起点的 x 和 y 坐标。

- drawImage()：总共需要传入 9 个参数：要绘制的图像、源图像的 x 坐标、源图像的 y 坐标、源图像的宽度、源
图像的高度、目标图像的 x 坐标、目标图像的 y 坐标、目标图像的宽度、目标图像的高度。

#### 阴影
> 2D 上下文会根据以下几个属性的值，自动为形状或路径绘制出阴影。

- shadowColor：用 CSS 颜色格式表示的阴影颜色，默认为黑色。
- shadowOffsetX：形状或路径 x 轴方向的阴影偏移量，默认为 0。
- shadowOffsetY：形状或路径 y 轴方向的阴影偏移量，默认为 0。
- shadowBlur：模糊的像素数，默认 0，即不模糊。

#### 渐变
> 渐变由 CanvasGradient 实例表示，很容易通过 2D 上下文来创建和修改。要创建一个新的线性渐
变，可以调用 createLinearGradient()方法。这个方法接收 4 个参数：起点的 x 坐标、起点的 y 坐
标、终点的 x 坐标、终点的 y 坐标。

-  createLinearGradient()：线性渐变，这个方法接收 4 个参数：起点的 x 坐标、起点的 y 坐
标、终点的 x 坐标、终点的 y 坐标。
- createRadialGradient()：径向渐变，这个方法接收 6 个参
数，对应着两个圆的圆心和半径。前三个参数指定的是起点圆的原心（x 和 y）及半径，后三个参数指
定的是终点圆的原心（x 和 y）及半径。

#### 模式
> 模式其实就是重复的图像，可以用来填充或描边图形。

- createPattern()：传入两个参数：一个 HTML <img>元素和一个表示如何重复图像的字符串。
其中，第二个参数的值与 CSS 的 background-repeat 属性值相同

#### 使用图像数据
> 2D 上下文的一个明显的长处就是，可以通过 getImageData()取得原始图像数据。这个方法接收
4 个参数：要取得其数据的画面区域的 x 和 y 坐标以及该区域的像素宽度和高度。

- getImageData()：接收
4 个参数：要取得其数据的画面区域的 x 和 y 坐标以及该区域的像素宽度和高度。

> 返回的对象是 ImageData 的实例。每个 ImageData 对象都有三个属性：width、height 和
data。其中 data 属性是一个数组，保存着图像中每一个像素的数据。

#### 合成
> 还有两个会应用到 2D 上下文中所有绘制操作的属性：globalAlpha 和 globalCompositionOperation。

- globalAlpha：0 - 1之间的值
- globalCompositionOperation：
    - source-over（默认值）：后绘制的图形位于先绘制的图形上方。
    - source-in：后绘制的图形与先绘制的图形重叠的部分可见，两者其他部分完全透明。
    - source-out：后绘制的图形与先绘制的图形不重叠的部分可见，先绘制的图形完全透明。
    - source-atop：后绘制的图形与先绘制的图形重叠的部分可见，先绘制图形不受影响。
    - destination-over：后绘制的图形位于先绘制的图形下方，只有之前透明像素下的部分才可见。
    - destination-in：后绘制的图形位于先绘制的图形下方，两者不重叠的部分完全透明。
    - destination-out：后绘制的图形擦除与先绘制的图形重叠的部分。
    - destination-atop：后绘制的图形位于先绘制的图形下方，在两者不重叠的地方，先绘制的图形会变透明。
    - lighter：后绘制的图形与先绘制的图形重叠部分的值相加，使该部分变亮。
    - copy：后绘制的图形完全替代与之重叠的先绘制图形。
    - xor：后绘制的图形与先绘制的图形重叠的部分执行“异或”操作。