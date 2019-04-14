### Vue Router 源码学习笔记4 - pushState和replaceState的实现

#### push/replace 的实现
对于 HTML5History，push/replace 的实现，用了 util/push-state.js 中的 pushState/replaceState，方法内部依靠 window.history.pushState/window.history.replaceState 实现。

对于 HashHistory，需要判断当前浏览器是否支持 window.history.pushState，具体如下：
```
inBrowser && 
主流浏览器中的一个 &&
window.history && 
'pushState' in window.history
```
若是，处理方式与 HTML5History 相同。  
若否，直接操作 window.location 去改变 url。pushState 时，给 window.location.hash 赋值；replaceState 时，调用 window.location.replace()。

#### History.pushState() 与 History.replaceState()
MDN：
> pushState() 需要三个参数：一个状态对象， 一个标题 (目前被忽略)，和 (可选的) 一个URL。
> 
> replaceState() 的使用与 pushState() 非常相似，区别在于  replaceState()  是修改了当前的历史记录项而不是新建一个。 注意这并不会阻止其在全局浏览器历史记录中创建一个新的历史记录项。  

这两个方法都不会刷新页面。

#### 下一步
路由已经改变，接下来是更新视图。

#### 参考文档
> https://developer.mozilla.org/zh-CN/docs/Web/API/History_API