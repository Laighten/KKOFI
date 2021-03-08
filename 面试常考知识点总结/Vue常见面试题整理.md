> 1、Vue的响应式系统（即双向绑定）
>
> 2、介绍一下Vue的生命周期
>
> 3、computed与watch的区别
>
> 4、为什么组件的data必须是一个函数
>
> 5、组件之间通信的方式有哪些
>
> 6、Vue事件绑定原理说一下
>
> 7、slot是什么？有什么作用？原理是什么？
>
> 8、Vue模板渲染的原理是什么？
>
> 9、template预编译是什么？
>
> 10、template和JSX有什么区别？
>
> 11、说一下虚拟DOM
>
> 12、介绍一下Vue中的diff算法
>
> 13、key属性的作用是什么？
>
> 14、说说Vue2.0和Vue3.0有什么区别？
>
> 15、为什么要新增Composition API，它能解决什么问题？
>
> 16、都说Composition API与React Hook很像，说说区别？
>
> 17、SSR有了解吗？原理是什么？
>
> 参考资料：https://github.com/yacan8/blog/issues/31



## 答案总结：

#### 1、Vue的响应式系统（即双向绑定）

​	Vue为MVVM框架，当数据模型data变化时，页面视图会得到响应更新，其原理对data的getter/setter方法进行拦截（Object.defineProperty或者Proxy），利用发布订阅的设计模式，在getter方法中进行订阅，在setter方法中发布通知，让所有订阅者完成响应。

​	在响应式系统中，Vue会为数据模型data的每一个属性新建一个订阅中心作为发布者，而监听器watch、计算属性computed、视图渲染template/render三个角色同时作为订阅者，对于监听器watch，会直接订阅观察监听的属性，对于计算属性computed和视图渲染template/render，如果内部执行获取了data的某个属性，就会执行该属性的getter方法，然后自动完成

#### 2、介绍一下Vue的生命周期

​	1、初始化显示

​		beforeCreate()：是new Vue()之后触发的第一个钩子，在当前阶段data、methods、computed以及watch上的数据和方法都不能被访问。

​		Created()：在实例创建完成后发生，当前阶段已经完成了数据观测，也就是可以使用数据，更改数据，在这里更改数据不会触发updated函数。可以做一些初始数据的获取，在当前阶段无法与Dom进行交互，如果非要想，可以通过vm.$nextTick来访问Dom。

​		beforeMount()：发生在挂载之前，在这之前template模板已导入渲染函数编译。而当前阶段虚拟Dom已经创建完成，即将开始渲染。在此时也可以对数据进行更改，不会触发updated。

​		mounted()：在挂载完成后发生，在当前阶段，真实的Dom挂载完毕，数据完成双向绑定，可以访问到Dom节点，使用$refs属性对Dom进行操作。

​	2、更新显示

​		beforeUpdate()：发生在更新之前，也就是响应式数据发生更新，虚拟dom重新渲染之前被触发，你可以在当前阶段进行更改数据，不会造成重渲染。

​		updated()：发生在更新完成之后，当前阶段组件Dom已完成更新。要注意的是避免在此期间更改数据，因为这可能会导致无限循环的更新。

​	3、销毁Vue实例

​		beforeDestroy()：发生在实例销毁之前，在当前阶段实例完全可以被使用，我们可以在这时进行善后收尾工作，比如清除计时器。

​		destroyed()：发生在实例销毁之后，这个时候只剩下了dom空壳。组件已被拆解，数据绑定被卸除，监听被移出，子实例也统统被销毁。

#### 3、computed与watch的区别

#### 4、为什么组件的data必须是一个函数

#### 5、组件之间通信的方式有哪些

#### 6、Vue事件绑定原理说一下

#### 7、slot是什么？有什么作用？原理是什么？

#### 8、Vue模板渲染的原理是什么？

#### 9、template预编译是什么？

#### 10、template和JSX有什么区别？

#### 11、说一下虚拟DOM

#### 12、介绍一下Vue中的diff算法

#### 13、key属性的作用是什么？

#### 14、说说Vue2.0和Vue3.0有什么区别？

#### 15、为什么要新增Composition API，它能解决什么问题？

#### 16、都说Composition API与React Hook很像，说说区别？

#### 17、SSR有了解吗？原理是什么？