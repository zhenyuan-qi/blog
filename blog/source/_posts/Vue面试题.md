---

title: Vue面试题
date: 2022-03-18 13:54:55
tags: vue

---

- ### Vue 的生命周期

  `beforeCreate(){}`

   实例初始化之前，开始监控data数据，初始化vue内部事件,el、data未初始化； this.$el和this.$data 都获取不到

  `created(){}   ` 

   实例创建完成之后调用，完成了data数据的初始化，el没有，可以调用methods中的方法。this.$data能拿到，this.$el获取不到

  `beforeMount(){} ` 

   挂载开始之前被调用，相关render函数被调用（虚拟DOM）,完成了el和data的初始化，此时还没有挂载到页面。this.$data能拿到，this.$el获取不到

  `mounted(){}  `

   挂载完成，$el和$data都能获取到

  `beforeUpdata(){}`

   数据发生变化之前被调用，不会触发重渲染过程

  `updata(){}`

   数据更新后

  `beforeDestroy(){}`

   在实例销毁之前调用，实例仍然完全可用。做一些重置操作，清除组件定时器和监听dom事件实例销毁后调用

  `destroyed(){}`

   实例销毁后调用

- ### keep-alive是做什么用的

  keep-alive 是用来对组件进行缓存，从而节省性能。
  当keep-alive内被切换时，组件的activated、deactivated这个两个声明周期会执行。
  首次进入会执行`beforeCreate=>created=>beforeMount=>mounted=>activated`生命周期，
  第二（n）次进入只会执行activated;

  

- ### v-if和v-show的区别

  v-if 是通过控制dom节点的存在与否来控制元素的显示隐藏；
       使用场景：条件很少改变使用，有更高的切换消耗。
  v-show 是通过设置dom元素的display样式 block显示  none隐藏
       使用场景：频繁切换，有更高的初始化渲染消耗。

- ###   computed、methods、watch的区别

  三者默认加载顺序
  `computed=>watch,不执行methods`
  触发条件后加载顺序
  `computed=>methods=>watch`

  **computer属性 vs methods属性**
  computer计算属性 具有缓存，在dom加载后立即执行，
  methods 必须要有触发条件才会执行

  **computed计算属性 vs watch监听属性**
  computed 是依赖数据发生改变才会执行；
  watch 是监听属性发生改变才会执行。

  

  