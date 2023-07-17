# Vue3.0

## Vue3.0 有什么更新

（1）监测机制的改变

 3.0 将带来基于代理 Proxy的 observer 实现，提供全语⾔覆盖的反应性跟踪。 消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制： 

（2）只能监测属性，不能监测对象 

检测属性的添加和删除； 检测数组索引和⻓度的变更； ⽀持 Map、Set、WeakMap 和 WeakSet。

（3）模板 

* 作⽤域插槽，2.x 的机制导致作⽤域插槽变了，⽗组件会重新渲染，⽽ 3.0 把作⽤域插槽改成了函 数的⽅式，这样只会影响⼦组件的重新渲染，提升了渲染的性能。 
* 同时，对于 render 函数的⽅⾯，vue3.0 也会进⾏⼀系列更改来⽅便习惯直接使⽤ api 来⽣成 vdom 。

（4）对象式的组件声明⽅式 

* vue2.x 中的组件是通过声明的⽅式传⼊⼀系列 option，和 TypeScript 的结合需要通过⼀些装饰器 的⽅式来做，虽然能实现功能，但是⽐较麻烦。
*  3.0 修改了组件的声明⽅式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易

（5）其它⽅⾯的更改 

* ⽀持⾃定义渲染器，从⽽使得 weex 可以通过⾃定义渲染器的⽅式来扩展，⽽不是直接 fork 源码来 改的⽅式。 ⽀持
* Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对⼀些特殊的 场景做了处理。 
* 基于 tree shaking 优化，提供了更多的内置功能。



## defineProperty 和 proxy 的区别

Vue 在实例初始化时遍历 data 中的所有属性，并使⽤ Object.defineProperty 把这些属性全部转为 getter/setter。这样当追踪数据发⽣变化时，setter 会被⾃动调⽤。 

Object.defineProperty 是 ES5 中⼀个⽆法 shim 的特性，这也就是 Vue 不⽀持 IE8 以及更低版本浏 览器的原因。 



但是这样做有以下问题：

1. 添加或删除对象的属性时，Vue 检测不到。因为添加或删除的对象没有在初始化进⾏响应式处理， 只能通过 $set 来调⽤ Object.defineProperty() 处理。 
2. ⽆法监控到数组下标和⻓度的变化。 



Vue3 使⽤ Proxy 来监控数据的变化。Proxy 是 ES6 中提供的功能，其作⽤为：⽤于定义基本操作的 ⾃定义⾏为（如属性查找，赋值，枚举，函数调⽤等）。相对于 Object.defineProperty() ，其有 以下特点：

1. Proxy 直接代理整个对象⽽⾮对象属性，这样只需做⼀层代理就可以监听同级结构下的所有属性变 化，包括新增属性和删除属性。
2.  Proxy 可以监听数组的变化。



## Vue3.0 为什么要用 proxy

在 Vue2 中， Object.defineProperty 会改变原始数据，⽽ Proxy 是创建对象的虚拟表示，并提供 set 、get 和 deleteProperty 等处理器，这些处理器可在访问或修改原始对象上的属性时进⾏拦截，有以下 特点∶ 

* 不需⽤使⽤` Vue.$set` 或 `Vue.$delete` 触发响应式。 
* 全⽅位的数组变化检测，消除了Vue2 ⽆效的边界情况。
* ⽀持 Map，Set，WeakMap 和 WeakSet。

Proxy 实现的响应式原理与 Vue2的实现原理相同，实现⽅式⼤同⼩异∶ 

* get 收集依赖
* Set、delete 等触发依赖 
* 对于集合类型，就是对集合对象的⽅法做⼀层包装：原⽅法执⾏后执⾏依赖相关的收集或触发逻辑。



## Vue3.0 中的 Vue Composition API

在 Vue2 中，代码是 Options API ⻛格的，也就是通过填充 (option) data、methods、computed 等 属性来完成⼀个 Vue 组件。这种⻛格使得 Vue 相对于 React极为容易上⼿，同时也造成了⼏个问题： 

1. 由于 Options API 不够灵活的开发⽅式，使得Vue开发缺乏优雅的⽅法来在组件间共⽤代码。 
2. Vue 组件过于依赖 this 上下⽂，Vue 背后的⼀些⼩技巧使得 Vue 组件的开发看起来与 JavaScript 的开发原则相悖，⽐如在 methods 中的 this 竟然指向组件实例来不指向 methods 所在的对象。这也使得 TypeScript 在Vue2 中很不好⽤。 

于是在 Vue3 中，舍弃了 Options API，转⽽投向 Composition API。Composition API本质上是将 Options API 背后的机制暴露给⽤户直接使⽤，这样⽤户就拥有了更多的灵活性，也使得 Vue3 更适合 于 TypeScript 结合。

 如下，是⼀个使⽤了 Vue Composition API 的 Vue3 组件：

```vue
<template>
  <button @click="increment">Count: {{ count }}</button>
</template>
<script>
// Composition API 将组件属性暴露为函数，因此第⼀步是导⼊所需的函数
import { ref, computed, onMounted } from "vue";
export default {
  setup() {
    // 使⽤ ref 函数声明了称为 count 的响应属性，对应于Vue2中的data函数
    const count = ref(0);
    // Vue2中需要在methods option中声明的函数，现在直接声明
    function increment() {
      count.value++;
    }
    // 对应于Vue2中的mounted声明周期
    onMounted(() => console.log("component mounted!"));
    return {
      count,
      increment,
    };
  },
};
</script>

```

显⽽易⻅，Vue Composition API 使得 Vue3 的开发⻛格更接近于原⽣ JavaScript，带给开发者更多 地灵活性



### Composition API 与 React Hook 很像，区别是什么

从React Hook的实现⻆度看，React Hook是根据useState调⽤的顺序来确定下⼀次重渲染时的state是 来源于哪个useState，所以出现了以下限制 

* 不能在循环、条件、嵌套函数中调⽤Hook 
* 必须确保总是在你的React函数的顶层调⽤Hook 
* useEffect、useMemo等函数必须⼿动确定依赖关系 

⽽Composition API是基于Vue的响应式系统实现的，与React Hook的相⽐ 

* 声明在setup函数内，⼀次组件实例化只调⽤⼀次setup，⽽React Hook每次重渲染都需要调⽤ Hook，使得React的GC⽐Vue更有压⼒，性能也相对于Vue来说也较慢
* Compositon API的调⽤不需要顾虑调⽤顺序，也可以在循环、条件、嵌套函数中使⽤ 
* 响应式系统⾃动实现了依赖收集，进⽽组件的部分的性能优化由Vue内部⾃⼰完成，⽽React Hook 需要⼿动传⼊依赖，⽽且必须必须保证依赖的顺序，让useEffect、useMemo等函数正确的捕获依 赖变量，否则会由于依赖不正确使得组件性能下降。 

虽然Compositon API看起来⽐React Hook好⽤，但是其设计思想也是借鉴React Hook的。