### Vue Router 源码学习笔记2 - 三种 History

#### 三种 History
HashHistory、HTML5History 和 AbstractHistory。

#### 应用环境

AbstractHistory 用在非浏览器环境（node），HashHistory 和 HTML5History 用在浏览器环境。

#### 共同之处
都实现了 history/base.js 中的 push、replace、go 和 getCurrentLocation 接口。  
对于 HashHistory 和 HTML5History，push、replace 和 go 分别基于原生方法 history.pushState 、history.replaceState 和 history.go 实现。

#### HashHistory 与 HTML5History
###### 颜值的不同
HashHistory 对应的 hash 模式下 url 带 #，HTML5History 对应的 history 下 url 不带 #。

###### 侦听事件的不同
HashHistory 中，对于支持 history.pushState 的环境，侦听 popstate 事件；不支持的，侦听 hashchange 事件。
HTML5History 中，侦听 popstate 事件。

#### window 的 popstate 事件
MDN：
> 调用 history.pushState() 或者 history.replaceState() 不会触发 popstate 事件。   
popstate 事件只会在浏览器某些行为下触发, 比如点击后退、前进按钮（或者在 JavaScript 中调用 history.back()、history.forward()、history.go()  方法）。

#### window 的 hashchange 事件
MDN：
> 当一个窗口的 hash（URL 中 # 后面的部分）改变时就会触发 hashchange 事件（参见 location.hash）。


#### 下一步
popstate 或 hashchange 被触发之后，到底发生了什么，需要去了解 history/base.js 中的 transitionTo。