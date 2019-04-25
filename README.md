# vue-router
vue-路由守卫

beforeEach(to,from,next)

全局前置守卫，接收三个参数

· to： 即将要进入的目标 路由对象

· from: 当前导航正要离开的路由

· next:  调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

    next():进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed 
  
    next(false): 中断当前的导航。如果浏览器的 URL 改变了，那么 URL 地址会重置到 from 路由对应的地址。
  
    next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。
    
    next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。
    
确保要调用 next 方法，否则钩子就不会被 resolved。

代码示例

    import Vue from 'vue'
    import Router from 'vue-router'
    import Index from '../components/index'
    import Login from '../components/login'
    import store from '../store/index'

    Vue.use(Router)

    const router = new Router({
    routes: [
    {
      path: '/index',
      name: 'index',
      component: Index
    },
    {
      path: '/login',
      name: 'login',
      component: Login
    }
    ]
    })

    router.beforeEach((to, from, next) => {
    
    //localstorage判断是否已登录
    if (window.localStorage.getItem('user')) {
       store.commit('setUser', window.localStorage.getItem('user'))
    }
    
    //未登录跳转到login
    if (!store.getters.getUser) {
        if (to.name !== 'login') {
           next('/login')
        }
    }
    
    //已登录不做操作
    next()
    })

    export default router
    
    
·-------------


afterEach(to,from)

全局后置钩子

和守卫不同的是，这些钩子不会接受 next 函数也不会改变导航本身：

    router.afterEach((to, from) => {
      // do someting
    })
    
-------------

beforeEnter(to, from, next)

路由独享守卫

    const router = new VueRouter({
    routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
    ]
    })
    
-------------

组件内的守卫

· beforeRouteEnter
· beforeRouteUpdate (2.2 新增)
· beforeRouteLeave

    const Foo = {
      template: `...`,
      beforeRouteEnter (to, from, next) {
         // 在渲染该组件的对应路由被 confirm 前调用
         // 不！能！获取组件实例 `this`
         // 因为当守卫执行前，组件实例还没被创建
    },
    beforeRouteUpdate (to, from, next) {
         // 在当前路由改变，但是该组件被复用时调用
         // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
         // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
         // 可以访问组件实例 `this`
    },
    beforeRouteLeave (to, from, next) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
    }
    }


-------------

导航解析流程

1、导航被触发。

2、在失活的组件里调用离开守卫。

3、调用全局的 beforeEach 守卫。

4、在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。

5、在路由配置里调用 beforeEnter。

6、解析异步路由组件。

7、在被激活的组件里调用 beforeRouteEnter。

8、调用全局的 beforeResolve 守卫 (2.5+)。

9、导航被确认。

10、调用全局的 afterEach 钩子。

11、触发 DOM 更新。

12、用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。
