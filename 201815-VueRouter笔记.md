#### 基础知识点

1. 可以在组件内部用 this.$router 访问路由器，用 this.$route 访问当前的路由。
2. 路径参数用冒号标记，$route.params 可以访问到这些参数。
3. 路由参数改变，由于组件复用，组件的生命周期钩子不会再次被调用。想在路由参数变化时做点什么，可以 watch $route 对象，或者用 beforeRouteUpdate。
4. 一个路径可以匹配多个路由，先定义的优先级高。
5. 使用 $router.push 或 $router.replace 都会在当前页打开新链接。想要在新 tab 中打开链接，用 $router.resolve 解析处链接地址，然后用 window.open(href, '_blank') 打开。
6. 可以用 name 为某个路由设置名称，可以是中文。
7. 同级可以有多个 router view，以 name 区分，没定义 name 的默认为 default。
8. 重定向可以指定某个 router 指向另一个 router，例如将空白 router '/'指向 homepage。
9. 别名可以让某个 router 有另一个访问方式，例如给嵌套关系复杂的路由一个简洁的地址。
10. 路由组件可用 props 传参：传 true 标示接收 $route.params 为参数；传对象就接受这个对象为参数；传 function 则接收返回的对象为参数，可以在 function 中包装想要的参数。
11. router 的 History 可以让 URL 中不再有 #，需要在服务端配置匹配不到的 URL 返回 app 依赖的 html 页面（在 router 中处理 404 的情况），另外还需要浏览器支持 HTML5 History API。

#### 导航解析流程

1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的 beforeEach 守卫。意思就是 before every router。
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。重用的组件，不会重新调用 mounted。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。就是从外部加载进来的那些组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

#### 路由元信息
每个路由可以被定义元信息 meta，里面可以存储一些配置。

#### 滚动行为
配置路由触发后，页面的滚动位置可用 scrollBehavior