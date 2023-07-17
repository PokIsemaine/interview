# Vue 基础

## Vue的基本原理

当⼀个Vue实例创建时，Vue会遍历data中的属性，⽤ Object.defineProperty（vue3.0使⽤proxy ）将 它们转为 getter/setter，并且在内部追踪相关依赖，在属性被访问和修改时通知变化。 每个组件实例都有相应的 watcher 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被 调⽤时，会通知watcher重新计算，从⽽致使它关联的组件得以更新。

![image.png](https://s2.loli.net/2023/04/27/AscaCNv7VSDdewJ.png)

## 双向数据绑定的原理

Vue.js 是采⽤数据劫持结合发布者-订阅者模式的⽅式，通过Object.defineProperty()来劫持各个属性 的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。主要分为以下几个步骤： 

1. 需要observe的数据对象进⾏递归遍历，包括⼦属性对象的属性，都加上setter和getter这样的话， 给这个对象的某个值赋值，就会触发setter，那么就能监听到了数据变化 
2. compile解析模板指令，将模板中的变量替换成数据，然后初始化渲染⻚⾯视图，并将每个指令对应 的节点绑定更新函数，添加监听数据的订阅者，⼀旦数据有变动，收到通知，更新视图 
3. Watcher订阅者是Observer和Compile之间通信的桥梁，主要做的事情是: ①在⾃身实例化时往属性 订阅器(dep)⾥⾯添加⾃⼰ ②⾃身必须有⼀个update()⽅法 ③待属性变动dep.notice()通知时，能调 ⽤⾃身的update()⽅法，并触发Compile中绑定的回调，则功成身退。 
4. MVVM作为数据绑定的⼊⼝，整合Observer、Compile和Watcher三者，通过Observer来监听⾃⼰ 的model数据变化，通过Compile来解析编译模板指令，最终利⽤Watcher搭起Observer和Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) -> 数据model变更的双向绑定 效果。

![image.png](https://s2.loli.net/2023/04/27/E5UDI2JjpobVxqw.png)



## 使⽤ Object.defineProperty() 来进⾏数据劫持有什么缺点？

在对⼀些属性进⾏操作时，使⽤这种⽅法⽆法拦截，⽐如通过下标⽅式修改数组数据或者给对象新增属 性，这都不能触发组件的重新渲染，因为 Object.defineProperty 不能拦截到这些操作。更精确的来 说，对于数组⽽⾔，⼤部分操作都是拦截不到的，只是 Vue 内部通过重写函数的⽅式解决了这个问题。 在 Vue3.0 中已经不使⽤这种⽅式了，⽽是通过使⽤ Proxy 对对象进⾏代理，从⽽实现数据劫持。使⽤ Proxy 的好处是它可以完美的监听到任何⽅式的数据改变，唯⼀的缺点是兼容性的问题，因为 Proxy 是 ES6 的语法。



## MVVM、MVC、MVP的区别

MVC、MVP 和 MVVM 是三种常见的软件架构设计模式，主要通过分离关注点的⽅式来组织代码结构， 优化开发效率。 在开发单⻚⾯应⽤时，往往⼀个路由⻚⾯对应了⼀个脚本⽂件，所有的⻚⾯逻辑都在⼀个脚本⽂件⾥。 ⻚⾯的渲染、数据的获取，对⽤户事件的响应所有的应⽤逻辑都混合在⼀起，这样在开发简单项⽬时，可能看不出什么问题，如果项⽬变得复杂，那么整个⽂件就会变得冗⻓、混乱，这样对项⽬开发和后期 的项⽬维护是⾮常不利的。

（1）MVC 

MVC 通过分离 Model、View 和 Controller 的⽅式来组织代码结构。其中 View 负责⻚⾯的显示逻 辑，Model 负责存储⻚⾯的业务数据，以及对相应数据的操作。并且 View 和 Model 应⽤了观察者模 式，当 Model 层发⽣改变的时候它会通知有关 View 层更新⻚⾯。Controller 层是 View 层和 Model 层的纽带，它主要负责⽤户与应⽤的响应操作，当⽤户与⻚⾯产⽣交互的时候，Controller 中的事件触 发器就开始⼯作了，通过调⽤ Model 层，来完成对 Model 的修改，然后 Model 层再去通知 View 层 更新。 

![image.png](https://s2.loli.net/2023/04/27/ENGeSKktlFXngDr.png)

（2）MVVM 

MVVM 分为 Model、View、ViewModel： 

* Model代表数据模型，数据和业务逻辑都在Model层中定义； 
* View代表UI视图，负责数据的展示； ViewModel负责监听Model中数据的改变并且控制视图的更新，处理⽤户交互操作； 
* Model和View并⽆直接关联，⽽是通过ViewModel来进⾏联系的，Model和ViewModel之间有着双向数 据绑定的联系。

因此当Model中的数据改变时会触发View层的刷新，View中由于⽤户交互操作⽽改变的 数据也会在Model中同步。 这种模式实现了 Model和View的数据⾃动同步，因此开发者只需要专注于数据的维护操作即可，⽽不需 要⾃⼰操作DOM。

![image.png](https://s2.loli.net/2023/04/27/1b8faOhPVICL7SG.png)

（3）MVP 

MVP 模式与 MVC 唯⼀不同的在于 Presenter 和 Controller。在 MVC 模式中使⽤观察者模式，来实现 当 Model 层数据发⽣变化的时候，通知 View 层的更新。这样 View 层和 Model 层耦合在⼀起，当项 ⽬逻辑变得复杂的时候，可能会造成代码的混乱，并且可能会对代码的复⽤性造成⼀些问题。MVP 的 模式通过使⽤ Presenter 来实现对 View 层和 Model 层的解耦。MVC 中的Controller 只知道 Model 的接⼝，因此它没有办法控制 View 层的更新，MVP 模式中，View 层的接⼝暴露给了 Presenter 因此 可以在 Presenter 中将 Model 的变化和 View 的变化绑定在⼀起，以此来实现 View 和 Model 的同步 更新。这样就实现了对 View 和 Model 的解耦，Presenter 还包含了其他的响应逻辑。



## Computed 和 Watch 的区别

对于Computed： 

* 它⽀持缓存，只有依赖的数据发⽣了变化，才会重新计算
* 不⽀持异步，当Computed中有异步操作时，⽆法监听数据的变化 
* computed的值会默认⾛缓存，计算属性是基于它们的响应式依赖进⾏缓存的，也就是基于data声 明过，或者⽗组件传递过来的props中的数据进⾏计算的。 
* 如果⼀个属性是由其他属性计算⽽来的，这个属性依赖其他的属性，⼀般会使⽤computed 
* 如果computed属性的属性值是函数，那么默认使⽤get⽅法，函数的返回值就是属性的属性值；
* 在 computed中，属性有⼀个get⽅法和⼀个set⽅法，当数据发⽣变化时，会调⽤set⽅法。

 

对于Watch： 

* 它不⽀持缓存，数据变化时，它就会触发相应的操作 
* ⽀持异步监听 
* 监听的函数接收两个参数，第⼀个参数是最新的值，第⼆个是变化之前的值 
* 当⼀个属性发⽣变化时，就需要执⾏相应的操作 
* 监听数据必须是data中声明的或者⽗组件传递过来的props中的数据，当发⽣变化时，会触发其他 操作，函数有两个的参数：
    *  immediate：组件加载⽴即触发回调函数 
    * deep：深度监听，发现数据内部的变化，在复杂数据类型中使⽤，例如数组中的对象发⽣变 化。需要注意的是，deep⽆法监听到数组和对象内部的变化。

当想要执⾏异步或者昂贵的操作以响应不断的变化时，就需要使⽤watch。



总结：

* computed 计算属性 : 依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值 发⽣改变，下⼀次获取 computed 的值时才会重新计算 computed 的值。 
* watch 侦听器 : 更多的是观察的作⽤，⽆缓存性，类似于某些数据的监听回调，每当监听的数 据变化时都会执⾏回调进⾏后续操作。

 运⽤场景：

* 当需要进⾏数值计算,并且依赖于其它数据时，应该使⽤ computed，因为可以利⽤ computed 的缓存特性，避免每次获取值时都要重新计算。 
* 当需要在数据变化时执⾏异步或开销较⼤的操作时，应该使⽤ watch，使⽤ watch 选项允许 执⾏异步操作 ( 访问⼀个 API )，限制执⾏该操作的频率，并在得到最终结果前，设置中间状 态。这些都是计算属性⽆法做到的。