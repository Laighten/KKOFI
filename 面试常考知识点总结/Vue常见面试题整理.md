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
> 18、v-show和v-if的区别
>
> 参考资料：https://github.com/yacan8/blog/issues/31



## 答案总结：

#### 1、Vue的响应式系统（即双向绑定）

​	<font color=red>通过数据劫持监听加发布者-订阅者模式实现。我们在Observer中使用Object.defineProperty（或者proxy）函数来实现对数据的追踪。在该函数中有一个get方法和set方法，当外界通过Watcher读取数据时会触发get，进而将每个数据对应的依赖watcher添加到Dep数组中，当外界数据发生改变时，就会触发set函数，找到Dep中的依赖从而通知订阅者更新相应的页面。	</font>

​	Vue为MVVM框架，当数据模型data变化时，页面视图会得到响应更新，其原理对data的getter/setter方法进行拦截（Object.defineProperty或者Proxy），利用发布订阅的设计模式，在getter方法中进行订阅，在setter方法中发布通知，让所有订阅者完成响应。

​	在响应式系统中，Vue会为数据模型data的每一个属性新建一个订阅中心作为发布者，而监听器watch、计算属性computed、视图渲染template/render三个角色同时作为订阅者，对于监听器watch，会直接订阅观察监听的属性，对于计算属性computed和视图渲染template/render，如果内部执行获取了data的某个属性，就会执行该属性的getter方法，然后自动完成。

#### 2、介绍一下Vue的生命周期

​	1、初始化显示

​		beforeCreate()：是new Vue()之后触发的第一个钩子，在当前阶段data、methods、computed以及watch上的数据和方法都不能被访问。

> beforeCreate()：是new Vue()之后触发的第一个勾子函数，当前阶段数据和方法都不能被访问

​		Created()：在实例创建完成后发生，当前阶段已经完成了数据观测，也就是可以使用数据，更改数据，在这里更改数据不会触发updated函数。可以做一些初始数据的获取，在当前阶段无法与Dom进行交互，如果非要想，可以通过vm.$nextTick来访问Dom。

> Created()：在实例创建完成后发生，当前阶段已经完成了数据的观测，可以使用数据，更改数据且不会触发updated函数，但是当前阶段无法与DOM交互

​		beforeMount()：发生在挂载之前，在这之前template模板已导入渲染函数编译。而当前阶段虚拟Dom已经创建完成，即将开始渲染。在此时也可以对数据进行更改，不会触发updated。、

> 发生在挂载之前，template模板已经被编译成了render函数，且虚拟DOM已经创建完成，即将开始渲染，此时更改数据，不会触发updated

​		mounted()：在挂载完成后发生，在当前阶段，真实的Dom挂载完毕，数据完成双向绑定，可以访问到Dom节点，使用$refs属性对Dom进行操作。

> 真实DOM已经挂载完毕，数据完成双向绑定，可以访问到DOM节点

​	2、更新显示

​		beforeUpdate()：发生在更新之前，也就是响应式数据发生更新，虚拟dom重新渲染之前被触发，你可以在当前阶段进行更改数据，不会造成重渲染。

> 在响应式数据发生更新、虚拟DOM重新渲染之前被触发

​		updated()：发生在更新完成之后，当前阶段组件Dom已完成更新。要注意的是避免在此期间更改数据，因为这可能会导致无限循环的更新。

> 发生在更新完成之后，当前阶段组件的DOM已经完成更新，需要避免在此期间更改数据

​	3、销毁Vue实例

​		beforeDestroy()：发生在实例销毁之前，在当前阶段实例完全可以被使用，我们可以在这时进行善后收尾工作，比如清除计时器。

​		destroyed()：发生在实例销毁之后，这个时候只剩下了dom空壳。组件已被拆解，数据绑定被卸除，监听被移出，子实例也统统被销毁。

#### 3、computed与watch的区别

​	计算属性computed和监听器watch都可以观察属性的变化从而做出响应，不同的是：

​	计算属性computed更多是作为**缓存功能**的观察者，它可以将一个或者多个data的属性进行复杂的计算生成一个新的值，提供给渲染函数使用，当依赖的属性变化时，computed不会立即重新计算生成新的值，而是先标记为脏数据，当下次computed被获取时候，才会进行重新计算并返回。

​	而监听器watch并**不具备缓存性**，监听器watch提供一个监听函数，当监听的属性发生变化时，会立即执行该函数。

#### 4、为什么组件的data必须是一个函数

​	一个组件可能在很多地方使用，也就是会创建很多个实例，如果data是一个对象的话，对象是引用类型，一个实例修改了data会影响到其他实例，所以data必须使用函数，为每一个实例创建一个属于自己的data，**使其同一个组件的不同实例互不影响。**

#### 5、组件之间通信的方式有哪些

1. props，此方法只能用于父组件向子组件传递数据
2. Vue的自定义事件：
   - 通过v-on绑定
   - 通过$on绑定
   - 此方法仅适用于子组件向父组件传递消息
3. PubSubJS库（使用消息的订阅预发布），该方式可实现任意关系组件间的通信（数据）
4. 插槽：该方法用于父组件向子组件传递‘标签数据’
5. eventBus：每一个Vue实例都是一个Event Bus，都支持$on/$emit，可以为需要通信组件的实例之间new一个Vue实例，作为Event Bus进行通信。
6. Vuex：将状态和方法提取到Vuex，完成共享

#### 6、Vue事件绑定原理说一下

​	vue中的事件绑定是有两种，一种是原生的事件绑定，另一种是组件的事件绑定。原生的事件绑定在普通元素上是通过@click进行绑定，在组件上是通过@click.native进行绑定，组件中的nativeOn是等价于on的。组件的事件绑定的@click是vue 中自定义的 $on 方法来实现的，必须有$emit才可以触发。

```javascript
let compiler = require('vue-template-compiler'); // vue loader中的包
let r1 = compiler.compile('<div @click="fn()"></div>'); // 给普通标签绑定click事件
// 给组件绑定一个事件，有两种绑定方法
// 一种@click.native，这个绑定的就是原生事件
// 另一种@click，这个绑定的就是组件自定义事件
let r2 = compiler.compile('<my-component @click.native="fn" @click="fn1"></mycomponent>');
console.log(r1.render); // {on:{click}} 
console.log(r2.render); // {nativeOn:{click},on:{click}}
// 为什么组件要加native？因为组件最终会把nativeOn属性放到on的属性中去，这个on会单独处理
// 组件中的nativeOn 等价于 普通元素on，组件on会单独处理
```

1.原生的事件绑定，原生 dom 事件的绑定,采用的是 addEventListener 实现。

2.组件的事件绑定，组件绑定事件采用的是 $on 方法 。		

​	**原生事件的绑定是通过addEventLister绑定给真实的元素的；组件事件绑定是通过vue自定义的$on实现的。**如果要在组件上使用原生事件，需要加.native修饰符，这样就相当于在父组件中把子组件当做普通html标签，然后加上原生事件。

​	$on、$emit是基于订阅观察者模式的，维护一个事件中心，on的时候将事件按名称存在事件中心里，称之为订阅者，然后emit将对应的事件进行发布，去执行事件中心里的对应的监听器。

​	每一个Vue实例都是一个Event Bus，当子组件被创建的时候，父组件将事件传递给子组件，子组件初始化的时候是有$on方法将事件注册到内部，在需要的时候使用$emit触发函数，而对于原生native事件，使用addEventListener绑定到真实的DOM元素上。

https://blog.csdn.net/qq_42072086/article/details/108063281

#### 7、slot是什么？有什么作用？原理是什么？

​	**插槽（Slot）是Vue提出来的一个概念，插槽用于将所携带的内容插入到之指定的位置，从而使模板分块具有模块化的特质和作用。插槽显不显示、怎样显示是由父组件来控制的，而插槽在哪里显示就由子组件来进行控制**

slot又分三类，默认插槽，具名插槽和作用域插槽。

- 默认插槽：又名匿名插槽（或单个插槽），当slot没有指定name属性值的时候一个默认显示插槽，一个组件内只有有一个匿名插槽。
- 具名插槽：带有具体名字的插槽，也就是带有name属性的slot，一个组件可以出现多个具名插槽。
- 作用域插槽：默认插槽、具名插槽的一个变体，可以是匿名插槽，也可以是具名插槽，该插槽的不同点是在子组件渲染作用域插槽时，可以**将子组件内部的数据传递给父组件，让父组件根据子组件的传递过来的数据决定如何渲染该插槽。**

**实现原理**：当子组件vm实例化时，获取到父组件传入的slot标签的内容，存放在vm.$slot中，默认插槽为vm.$slot.default，具名插槽为vm.$slot.xxx，xxx 为插槽名，当组件执行渲染函数时候，遇到slot标签，使用$slot中的内容进行替换，此时可以为插槽传递数据，若存在数据，则可称该插槽为作用域插槽。

https://www.jb51.net/article/150676.htm

#### 8、Vue模板渲染的原理是什么？

​	vue中的模板template无法被浏览器解析并渲染，因为这不属于浏览器的标准，不是正确的HTML语法，所有需要**将template转化成一个JavaScript函数，这样浏览器就可以执行这一个函数并渲染出对应的HTML元素，就可以让视图跑起来了，这一个转化的过程，就成为模板编译。**

模板编译又分三个阶段，解析（parse），优化（optimize），生成（generate），最终生成可执行函数render。

- parse阶段：使用大量的正则表达式对template字符串进行解析，将标签、指令、属性等转化为抽象语法树AST。
- optimize阶段：遍历AST，找到其中的一些静态节点并进行标记，方便在页面重渲染的时候进行diff比较时，直接跳过这一些静态节点，优化runtime的性能。
- generate阶段：将最终的AST转化为render函数字符串。

#### 9、template预编译是什么？

​	**对于 Vue 组件来说，模板编译只会在组件实例化的时候编译一次，生成渲染函数之后在也不会进行编译。**因此，编译对组件的 runtime 是一种性能损耗。

​	而模板编译的目的仅仅是将template转化为`render function`，这个过程，正好可以在项目构建的过程中完成，这样可以让实际组件在 runtime 时直接跳过模板渲染，进而提升性能，这个在项目构建的编译template的过程，就是预编译。

> 对于Vue组件来说，模板编译只会在组件实例化的时候编译一次，生成渲染函数之后就不会进行编译。而模板编译的目的仅仅是将template转换为render函数，而这个过程正好可以在项目构建的过程中完成，在项目构建的过程中完成编译template编译就叫做预编译。
>
> ​	比如webpack的vue-loader依赖了vue-template-compiler模块，在 webpack 构建过程中，将template预编译成 render 函数，在 runtime 可直接跳过模板编译过程。

#### 10、template和JSX有什么区别？

​	对于 runtime 来说，只需要保证组件存在 render 函数即可，而我们有了预编译之后，我们只需要保证构建过程中生成 render 函数就可以。

​	在 webpack 中，我们使用`vue-loader`编译.vue文件，内部依赖的`vue-template-compiler`模块，在 webpack 构建过程中，将template预编译成 render 函数。

​	与 react 类似，在添加了jsx的语法糖解析器`babel-plugin-transform-vue-jsx`之后，就可以直接手写render函数。

​	所以，template和jsx的都是render的一种表现形式，不同的是：

​	JSX相对于template而言，具有**更高的灵活性**，**在复杂的组件中，更具有优势**，而 template 虽然显得有些呆滞。但是 template 在代码结构上更符合视图与逻辑分离的习惯，更简单、更直观、更好维护。

> 总结，区别就是：
>
> - JSX相对于template而言具有更高的灵活性，在复杂的组件中，更具优势。
> - template相对于JSX而言，在代码结构上更符合视图逻辑，更加简洁易维护。  

#### 11、说一下虚拟DOM

​	Virtual DOM 是 DOM 节点在 JavaScript 中的一种抽象数据结构，之所以需要虚拟DOM，是因为浏览器中操作DOM的代价比较昂贵，频繁操作DOM会产生性能问题。虚拟DOM的作用是在每一次响应式数据发生变化引起页面重渲染时，Vue对比更新前后的虚拟DOM，匹配找出尽可能少的需要更新的真实DOM，从而达到提升性能的目的。

#### 12、介绍一下Vue中的diff算法

在新老虚拟DOM对比时

- 首先，对比节点本身，判断是否为同一节点，如果不为相同节点，则删除该节点重新创建节点进行替换
- 如果为相同节点，进行patchVnode，判断如何对该节点的子节点进行处理，先判断一方有子节点一方没有子节点的情况(如果新的children没有子节点，将旧的子节点移除)
- 比较如果都有子节点，则进行updateChildren，判断如何对这些新老节点的子节点进行操作（diff核心）。
- 匹配时，找到相同的子节点，递归比较子节点

**在diff中，只对同层的子节点进行比较，放弃跨级的节点比较，使得时间复杂从`O(n^3)`降低值`O(n)`，也就是说，只有当新旧children都为多个子节点时才需要用核心的Diff算法进行同层级比较。**

#### 13、key属性的作用是什么？

​	在对节点进行diff的过程中，判断是否为相同节点的一个很重要的条件是key是否相等，如果是相同节点，则会尽可能的复用原有的DOM节点。所以key属性是提供给框架在diff的时候使用的，而非开发者。

#### 14、说说Vue2.0和Vue3.0有什么区别？

1. 重构响应式系统，使用Proxy替换Object.defineProperty，使用Proxy优势：

   - 可直接监听数组类型的数据变化

   - 监听的目标为对象本身，不需要像Object.defineProperty一样遍历每个属性，有一定的性能提升

   - 可拦截apply、ownKeys、has等13种方法，而Object.defineProperty不行

   - 直接实现对象属性的新增/删除

2. 新增Composition API，更好的逻辑复用和代码组织

3. 重构 Virtual DOM

   - 模板编译时的优化，将一些静态节点编译成常量

   - slot优化，将slot编译为lazy函数，将slot的渲染的决定权交给子组件

   - 模板中内联事件的提取并重用（原本每次渲染都重新生成内联函数）

4. 代码结构调整，更便于Tree shaking，使得体积更小

5. 使用Typescript替换Flow

#### 15、为什么要新增Composition API，它能解决什么问题？

​	Vue2.0中，随着功能的增加，组件变得越来越复杂，越来越难维护，而难以维护的根本原因是Vue的API设计迫使开发者使用watch，computed，methods选项组织代码，而不是实际的业务逻辑。

​	另外Vue2.0缺少一种较为简洁的低成本的机制来完成逻辑复用，虽然可以minxis完成逻辑复用，但是当mixin变多的时候，会使得难以找到对应的data、computed或者method来源于哪个mixin，使得类型推断难以进行。

​	所以Composition API的出现，主要是也是为了解决Option API带来的问题，第一个是代码组织问题，Compostion API**可以让开发者根据业务逻辑组织自己的代码，让代码具备更好的可读性和可扩展性**，也就是说当下一个开发者接触这一段不是他自己写的代码时，他可以更好的利用代码的组织反推出实际的业务逻辑，或者根据业务逻辑更好的理解代码。

​	**第二个是实现代码的逻辑提取与复用**，当然mixin也可以实现逻辑提取与复用，但是像前面所说的，多个mixin作用在同一个组件时，很难看出property是来源于哪个mixin，来源不清楚，另外，多个mixin的property存在变量命名冲突的风险。而Composition API刚好解决了这两个问题。

> 1、可以让开发者根据业务逻辑组织自己的代码，让代码具备更好的可读性和可扩展性。
>
> 2、可以实现代码的逻辑复用。

#### 16、都说Composition API与React Hook很像，说说区别？

从React Hook的实现角度看，React Hook是根据useState调用的顺序来确定下一次重渲染时的state是来源于哪个useState，所以出现了以下限制

- 不能在循环、条件、嵌套函数中调用Hook
- 必须确保总是在你的React函数的顶层调用Hook
- useEffect、useMemo等函数必须手动确定依赖关系

而Composition API是基于Vue的响应式系统实现的，与React Hook的相比

- 声明在setup函数内，一次组件实例化只调用一次setup，而React Hook每次重渲染都需要调用Hook，使得React的GC比Vue更有压力，性能也相对于Vue来说也较慢
- Compositon API的调用不需要顾虑调用顺序，也可以在循环、条件、嵌套函数中使用
- 响应式系统自动实现了依赖收集，进而组件的部分的性能优化由Vue内部自己完成，而React Hook需要手动传入依赖，而且必须必须保证依赖的顺序，让useEffect、useMemo等函数正确的捕获依赖变量，否则会由于依赖不正确使得组件性能下降。

虽然Compositon API看起来比React Hook好用，但是其设计思想也是借鉴React Hook的。

#### 17、SSR有了解吗？原理是什么？

​	在客户端请求服务器的时候，服务器到数据库中获取到相关的数据，并且在服务器内部将Vue组件渲染成HTML，并且将数据、HTML一并返回给客户端，这个在服务器将数据和组件转化为HTML的过程，**叫做服务端渲染SSR**。

而当客户端拿到服务器渲染的HTML和数据之后，由于数据已经有了，客户端不需要再一次请求数据，而只需要将数据同步到组件或者Vuex内部即可。除了数据意外，HTML也结构已经有了，客户端在渲染组件的时候，也只需要将HTML的DOM节点映射到Virtual DOM即可，不需要重新创建DOM节点，这个将数据和HTML同步的过程，又叫做客户端激活。

使用SSR的好处：

- 有利于SEO：其实就是有利于爬虫来爬你的页面，因为部分页面爬虫是不支持执行JavaScript的，这种不支持执行JavaScript的爬虫抓取到的非SSR的页面会是一个空的HTML页面，而有了SSR以后，这些爬虫就可以获取到完整的HTML结构的数据，进而收录到搜索引擎中。
- **白屏时间更短**：相对于客户端渲染，服务端渲染在浏览器请求URL之后已经得到了一个带有数据的HTML文本，浏览器只需要解析HTML，直接构建DOM树就可以。而客户端渲染，需要先得到一个空的HTML页面，这个时候页面已经进入白屏，之后还需要经过加载并执行 JavaScript、请求后端服务器获取数据、JavaScript 渲染页面几个过程才可以看到最后的页面。特别是在复杂应用中，由于需要加载 JavaScript 脚本，越是复杂的应用，需要加载的 JavaScript 脚本就越多、越大，这会导致应用的首屏加载时间非常长，进而降低了体验感。

更多详情查看[彻底理解服务端渲染 - SSR原理](https://github.com/yacan8/blog/issues/30)

#### 18、v-show和v-if的区别

- v-if：是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
- `v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
- `v-show` 不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
- 如果需要频繁切换 v-show 较好

#### 19、在vue整个生命周期中，数据是如何渲染视图的？

1. 把模板编译为render函数（在beforeMount之前，Created之后）

2. 实例进行挂载, 根据根节点render函数的调用，递归的生成虚拟dom（beforeMount阶段）

3. 对比虚拟dom，渲染到真实dom（Mounted之前）

4. 组件内部data发生变化，组件和子组件引用data作为props重新调用render函数，生成虚拟dom, 返回到步骤3。（Mounted阶段）

   ![img](./img/13119812-5890a846b6efa045.webp)

https://segmentfault.com/a/1190000018495383

#### 20、你所了解的vue中请求数据的方式？

1. vue-resource 官方提供的 vue的一个插件
2. axios
3. fetch-jsonp

https://www.jb51.net/article/181876.htm

#### 21、Vue路由的两种实现方式

​	前端不同页面的状态管理器，可以不向后台发送请求而直接通过前端技术实现多个页面的切换。

1. hash模式

   通过锚点定位原理进行无刷新跳转，触发后会在url后面多加一个#号

2. history模式

   使用HTML5中的historyAPI实现客户端路由模式，触发后url中没有#

#### 22、Vue中的this.$nextTick

​	**Vue.nextTick( [callback, context\] )](https://cn.vuejs.org/v2/api/#Vue-nextTick)**

- **参数**：

  - `{Function} [callback]`
  - `{Object} [context]`

- **用法**：

  **在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。**

  ```js
  // 修改数据
  vm.msg = 'Hello'
  // DOM 还没有更新
  Vue.nextTick(function () {
    // DOM 更新了
  })
  
  // 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
  Vue.nextTick()
    .then(function () {
      // DOM 更新了
    })
  ```
