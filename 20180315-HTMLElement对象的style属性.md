### HTMLElement 对象的 style 属性

#### HTMLElement
HTMLElement 接口表示所有的 HTML 元素。
> MDN:   
> The HTMLElement interface represents any HTML element. Some elements directly implement this interface, others implement it via an interface that inherits it.

所有的 HTML 元素都直接或间接的实现了 HTMLElement 接口。

#### style 属性

访问 style 属性，得到的 CSSStyleDeclaration 对象，相当于把内联样式字符串形式的 css 描述转成对象形式。  
举个例子： 

```
<div class="ball ball1" style="margin-left: 0;"></div>
```
内联样式： 
```
"margin-left: 0;"
```
CSSStyleDeclaration 对象：
```
{
  0: 'margin-left'
  ...
  cssText: 'margin-left: 0px;' // 0值在转换时会自动加单位，非零值不带单位会被认为无效
  ...
  marginLeft: '0px',
  ...
}
```
style 属性被定义为只读的。  
虽然 Chrome 等浏览器也允许你改动 style 属性，但因为无法赋予其有效值，以下语句相当于重置 style 属性：

```
var ball1 = document.querySelector('.ball1');
ball1.style = 'color: black;'；
ball1.style = '{color: "black"}'；
```
为什么说无法赋予其有效值呢？假如可以创建一个 CSSStyleDeclaration 对象不就是有效值了吗？  
试试： 
```
var newStyle = new CSSStyleDeclaration();
// Uncaught TypeError: Illegal constructor
```
原因如下：
> MDN:  
CSSStyleDeclaration is a read-only interface

#### 正确的 style 赋值方式
一次性设置多个样式：

```
ball1.style.cssText = 'color: black; border: 1px solid black;';
ball1.setAttribute('style', 'color: black; border: 1px solid black;');
```
单个样式设置：

```
ball1.style.color = 'black';
```
margin、width 之类的属性，记得要加单位：
```
ball1.style.marginLeft = '10px';
```

#### 一点心得
> 这是一个十分简单、十分基础的功能，但值得被记录的是学习这个知识点的思路。  
> 不要只是解决问题，而是要借助与问题相遇的契机，学到相关的知识。  
> 查询 MDN 文档的优势在于，能够了解当前知识的上游、下游，甚至直通 WC 规范。

#### 参考文档
> https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/style

#### 在线演示
> https://codepen.io/PennyOrAmy/full/MVaGYy