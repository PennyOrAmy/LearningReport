### Vue Router 源码学习笔记5 - 视图更新的实现

#### updateRoute 方法
History 的 updateRoute 方法，做了三件事情：
```
updateRoute (route: Route) {
  const prev = this.current
  // 更新 current router
  this.current = route
  // 执行 History 的 cb 方法
  this.cb && this.cb(route)
  // 调用全局的 afterEach 钩子
  this.router.afterHooks.forEach(hook => {
    hook && hook(route, prev)
  })
}
```
History 的 cb 方法从这里而来，可以理解为注册侦听器：
```
listen (cb: Function) {
  this.cb = cb
}
```
注册在 VueRouter.init（src/index.js）中完成：
```
history.listen(route => {
  this.apps.forEach((app) => {
    app._route = route
  })
})
```
可以看到，这里只是更新了 _route 属性，那视图为什么会更新呢？

#### 响应式属性
VueRouter 的 install 方法（src/install.js）中，全局 Vue 对象被做了如下混入：
```
Vue.mixin({
  beforeCreate () {
    if (isDef(this.$options.router)) {
      this._router = this.$options.router
      this._router.init(this)
      Vue.util.defineReactive(this, '_route', this._router.history.current)
    }
    registerInstance(this, this)
  },
})
```
可见 _route 被定义成了响应式属性，这意味着它发生改变的时候，会触发视图更新。
#### 响应式原理
##### 数据劫持
使用 Object.defineProperty
##### 设计模式
发布订阅模式

#### 下一步
试用 Object.defineProperty