### Vue Router 源码学习笔记3 - History 中的 transitionTo

#### 功能
transitionTo 方法的功能是路由跳转，它接收三个参数：
```
1. location：要转向的路由地址
2. onComplete：完成后的回调
3. onAbort：取消时的回调
```

#### 定义
History 类（history/base.js）中定义了 transitionTo。

#### 使用
以下的4个文件用到了 transitionTo：
```
1. VueRouter 类（index.js）的 init 方法，用来跳向当前浏览器地址中的路由
2. AbstractHistory 类（history/abstract.js）的 push、replace 方法，跳向目标路由
3. HashHistory 类（history/hash.js）的 push、replace 方法，跳向目标路由
4. HTML5History 类（history/html5.js）的 push、replace 方法，跳向目标路由
```

#### 分析
查看 transitionTo 方法，主要是调用了 confirmTransition 方法。confirmTransition 做了如下几件事情：
```
1. 如果目标路由与当前路由相同，取消跳转
2. 定义钩子队列，依次为：
   组件导航守卫 beforeRouteLeave -> 全局导航守卫 beforeHooks -> 组件导航守卫 beforeRouteUpdate -> 
   目标路由的 beforeEnter -> 处理异步组件 resolveAsyncComponents
3. 定义迭代器
4. 执行钩子队列
```
confirmTransition 执行完后，在其回调中调用了 updateRoute 方法、完成回调和 ensureURL 方法。  
其中 updateRoute 方法指定了当前 router，然后调用 afterEach 钩子。  

更加详细的过程，在官方文档中有描述：
>  1. 导航被触发。
>  2. 在失活的组件里调用离开守卫。
>  3. 调用全局的 beforeEach 守卫。
>  4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
>  5. 在路由配置里调用 beforeEnter。
>  6. 解析异步路由组件。
>  7. 在被激活的组件里调用 beforeRouteEnter。
>  8. 调用全局的 beforeResolve 守卫 (2.5+)。
>  9. 导航被确认。
> 10. 调用全局的 afterEach 钩子。
> 11. 触发 DOM 更新。
> 12. 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

#### 下一步
了解 pushState 和 replaceState 的底层实现。

#### 参考文档
> 导航守卫：https://router.vuejs.org/zh/guide/advanced/navigation-guards.html