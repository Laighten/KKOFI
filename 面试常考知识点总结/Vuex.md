### 一、Vuex底层原理

​	Vuex采用MVC模式中的Model层，规定所有的数据**必须通过action—>mutaion—>state**这个流程进行来改变状态的。再结合Vue的数据视图双向绑定实现页面的更新。统一页面状态管理，可以让复杂的组件交互变的简单清晰，同时在调试时也可以通过DEVtools去查看状态。

​	在当前前端的spa模块化项目中不可避免的是某些变量需要在全局范围内引用，此时父子组件的传值，子父组件间的传值，兄弟组件间的传值成了我们需要解决的问题。虽然vue中提供了props（父传子）commit（子传父）兄弟间也可以用localstorage和sessionstorage。但是这种方式在项目开发中带来的问题比他解决的问题（难管理，难维护，代码复杂，安全性低）更多。vuex的诞生也是为了解决这些问题，从而大大提高我们vue项目的开发效率。

![img](F:\Git仓库\前端面试真题总结\前端面试题整理\img\format,png)

其实他们原理时一样的，vuex中也有四个属性值：state、getters、mutations、actions。。

在没有actions的情况下：

- 数据：state --> data
- 获取数据：getters --> computed
- 更改数据：mutations --> methods

​    **视图通过点击事件，触发mutations中方法，可以更改state中的数据，一旦state数据发生更改，getters把数据反映到视图。**

> ​	actions作用是响应view上的用户输入导致的状态变化，只不过actions处理的是异步的操作，然后再同步提交给Mutations,再由mutations去改变state中的数据。

​	那么**actions,可以理解处理异步**，而单纯多加的一层。

​	既然提到了mutions actions这时候 就不得不提commit，dispatch这两个有什么作用呢？

​	在vue例子中，通过click事件，触发methods中的方法。当存在异步时，而在vuex中需要dispatch来触发actions中的方法，actions中的commit可以触发mutations中的方法。同步，则直接在组件中commit触发vuex中mutations中的方法。

![img](F:\Git仓库\前端面试真题总结\前端面试题整理\img\format2,png)

- **vuex的store是如何注入到组件中的？**

```js
//首先使用vuex,需要安装插件：
Vue.use(Vuex); // vue的插件机制,安装vuex插件
//当ues(Vuex)时候，会调用vuex中的install方法，装在vuex! 下面时install的核心源码：
Vue.mixin({
    beforeCreate() {
        if (this.$options && this.$options.store) {
            //找到根组件 main 上面挂一个$store
            this.$store = this.$options.store
            // console.log(this.$store);
        } else {
            //非根组件指向其父组件的$store
            this.$store = this.$parent && this.$parent.$store
        }
    }
})
```

​	store注入 vue的实例组件的方式，是**通过vue的 mixin机制，借助vue组件的生命周期 钩子 beforeCreate 完成的**。即 每个vue组件实例化过程中，会在 beforeCreate 钩子前调用 vuexInit 方法。

- **vuex的state和getters是如何映射到各个组件实例中响应式更新状态呢？**

```js
//实现getters原理
        let getters = options.getters || {}
        // console.log(getters);
        // this.getters = getters; //不是直接挂载到 getters上 这样只会拿到整个 函数体
        this.getters = {};
        // console.log(Object.keys(getters))  // ["myAge","myName"]
        Object.keys(getters).forEach((getterName) => {
            // console.log(getterName)  // myAge
            // 将getterName 放到this.getters = {}中
            // console.log(this.state);
            Object.defineProperty(this.getters, getterName, {
                // 当你要获取getterName（myAge）会自动调用get方法
                // 箭头函数中没有this               
                get: () => {
                    return getters[getterName](this.state)
                }
            })
        })
//从上面源码，我们可以看出Vuex的state状态是响应式，是借助vue的data是响应式，将state存入vue实例组件的data中；Vuex的getters则是借助vue的计算属性computed实现数据实时监听。
```

- #### mutations实现

  ```js
  let mutations = options.mutations || {}
  // console.log(mutations);
      this.mutations = {};
      Object.keys(mutations).forEach(mutationName=>{
          // console.log(mutationName);
  
          this.mutations[mutationName] = (payload) =>{
              this.mutations[mutationName](this.state,payload)
          }
  })
  ```

- #### actions实现

  ```js
  // actions的原理 
  let actions = options.actions || {}
      this.actions = {};
      forEach(actions,(actionName,value)=>{
          this.actions[actionName] = (payload)=>{
              value(this,payload)
          }
  })
  ```

- #### commit dispatch的实现

  ```js
  commit(type,payload){
      this.mutations[type](payload)
  }
  // type是actions的类型  
  dispatch=(type,payload)=>{
      this.actions[type](payload)
  }
  ```

参考：https://blog.csdn.net/weixin_44667072/article/details/101164766

参考：https://shq5785.blog.csdn.net/article/details/100763161