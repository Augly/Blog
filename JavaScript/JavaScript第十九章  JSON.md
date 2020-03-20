> 什么是Json：它是一种数据格式，是一种表示结构化数据的格式,不是一种编程语言。

#### 语法
> JSON 的语法可以表示以下三种类型的值。

- 简单值：使用与 JavaScript 相同的语法，可以在 JSON 中表示字符串、数值、布尔值和 null。
但 JSON 不支持 JavaScript 中的特殊值 undefined。
- 对象：对象作为一种复杂数据类型，表示的是一组无序的键值对儿。而每个键值对儿中的值可
以是简单值，也可以是复杂数据类型的值。
- 数组：数组也是一种复杂数据类型，表示一组有序的值的列表，可以通过数值索引来访问其中
的值。数组的值也可以是任意类型——简单值、对象或数组。

#### 解析与序列化
> Json可以解析成JavaScript对象，可互转，JSON 对象有两个方法：stringify()和 parse()。

##### JSON.stringify()
> 除了要序列化的 JavaScript 对象外，还可以接收另外两个参数，这两
个参数用于指定以不同的方式序列化 JavaScript 对象。第一个参数是个过滤器，可以是一个数组，也可
以是一个函数；第二个参数是一个选项，表示是否在 JSON 字符串中保留缩进。单独或组合使用这两个
参数，可以更全面深入地控制 JSON 的序列化。

- 过滤结果：如果过滤器参数是数组，那么 JSON.stringify()的结果中将只包含数组中列出的属性。来看下
面的例子。

示例:
```
var book = {
 "title": "Professional JavaScript",
 "authors": [
 "Nicholas C. Zakas"
 ],
 edition: 3,
 year: 2011
 };
var jsonText = JSON.stringify(book, ["title", "edition"]); 

```
以上代码在返回的结果jsonText字符串中，就只会包含 ["title", "edition"]这两个属性

#####  字符串缩进
> JSON.stringify()方法的第三个参数用于控制结果中的缩进和空白符。如果这个参数是一个数
值，那它表示的是每个级别缩进的空格数。

示例：
```
var book = {
 "title": "Professional JavaScript",
 "authors": [
 "Nicholas C. Zakas"
 ],
 edition: 3,
 year: 2011 
 }; 

 var jsonText = JSON.stringify(book, null, 4); 
```
以上代码表示缩进4个字符

#### 解析选项
> JSON.parse()方法也可以接收另一个参数，该参数是一个函数，将在每个键值对儿上调用。为了
区别 JSON.stringify()接收的替换（过滤）函数（replacer），这个函数被称为还原函数（reviver），
但实际上这两个函数的签名是相同的——它们都接收两个参数，一个键和一个值，而且都需要返回一
个值。

示例：
```
var book = {
 "title": "Professional JavaScript",
 "authors": [
 "Nicholas C. Zakas"
 ],
 edition: 3,
 year: 2011,
 releaseDate: new Date(2011, 11, 1)
 };
var jsonText = JSON.stringify(book); 
var bookCopy = JSON.parse(jsonText, function(key, value){
 if (key == "releaseDate"){
 return new Date(value);
 } else {
 return value;
 } 
});
alert(bookCopy.releaseDate.getFullYear()); 

```
以上代码先是为 book 对象新增了一个 releaseDate 属性，该属性保存着一个 Date 对象。这个
对象在经过序列化之后变成了有效的 JSON 字符串，然后经过解析又在 bookCopy 中还原为一个 Date
对象。还原函数在遇到"releaseDate"键时，会基于相应的值创建一个新的 Date 对象。结果就是
bookCopy.releaseDate 属性中会保存一个 Date 对象。正因为如此，才能基于这个对象调用
getFullYear()方法。

#### 总结

JSON 是一个轻量级的数据格式，可以简化表示复杂数据结构的工作量。JSON 使用 JavaScript 语法
的子集表示对象、数组、字符串、数值、布尔值和 null。即使 XML 也能表示同样复杂的数据结果，但
JSON 没有那么烦琐，而且在 JavaScript 中使用更便利。

ECMAScript 5 定义了一个原生的 JSON 对象，可以用来将对象序列化为 JSON 字符串或者将 JSON
数据解析为 JavaScript 对象。JSON.stringify()和 JSON.parse()方法分别用来实现上述两项功能。
这两个方法都有一些选项，通过它们可以改变过滤的方式，或者改变序列化的过程。

原生的 JSON 对象也得到了很多浏览器的支持，比如 IE8+、Firefox 3.5+、Safari 4+、Opera 10.5 和
Chrome。