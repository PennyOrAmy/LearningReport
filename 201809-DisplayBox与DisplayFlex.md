### display:box; 与 display: flex;

#### display:box; 是老版本的 display: flex;

display: box; 是 2009 年的语法，已经过时。  
display: flex; 是 2012 年的语法，也是标准语法。

#### 与子元素 display 方式的相关性不同

display:box; 作用下，如果子元素是 block 的，竖着排，如果子元素是 inline、inline-block 的，横着排。  
display:flex; 作用下，是横着排还是竖着排，只取决于 flex-direction 的值是 row 还是 column。

#### float 等属性是否受影响的情况不同

display:box; 作用下，float、clear、text-align、vertical-align 仍起作用。  
display:flex; 作用下，上述四属性失效。

#### 横向排列时，子元素之间空白字符（空格、换行等）处理不同
display:box; 作用下，不会被忽略。  
display:flex; 作用下，忽略。

##### 在线演示
> https://codepen.io/PennyOrAmy/full/qoKadM