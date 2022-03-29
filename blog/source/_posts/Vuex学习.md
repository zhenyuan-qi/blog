---
title: Vuex搞懂5大核心概念
date: 2022-03-21 18:02:38
tags: [vue,vuex]
---

#### Vuex是什么？

Vuex是全局状态管理器，集中式存储管理应用的所有组件状态。[官网解释](https://vuex.vuejs.org/zh/)

#### Vuex的核心概念

一、 **state**单一状态树，用一个对象就包含了全部的应用层状态；

```js
import {createApp} from "vue"
import {createStore} from "vuex"
const store = createStore({
    state(){
        count:0
    },
    getters:{},
    mutations:{
    	add(state){
    		state.count++
		}
	},
    actions:{},
    modules:{}
})
const app = createApp({/*根组件*/})
app.use(store)
```

可以通过`store.state`获取状态对象，`state.commit('add')`方法触发变更；

在Vue组件中,可以通过`this.$store`来获取状态对象，`this.$store.commit()`方法触发变更；

```js
methods:{
   add(){
        console.log(this.$store.state.count);
        this.$store.commit("add");
    }
}
```

当一个组件需要获取多个状态时，将这些状态都声明为计算属性会重复和冗余，我们可以使用`mapSate`辅助函数帮我们生成计算属性。

```js
import {mapState } from "vuex"
export default{
    computed:{
       	...mapState({
            count:state=>state.count,
            countAlias:'count',
            countState(state){
            	return state.count
            }
        }),
        value(){return 1}
    }
}
```

当映射的计算属行名称与state的子节点名称一致，可以传入一个字符串数组

```js
computed:{
	...mapState(["count"])
}
```

二、**Getter** 相当于vue中得计算属性，通过getter进一步处理，接受传参。

```js
import Vue from "vue"
import Vuex from "vuex"
Vue.use(Vuex)
export default new Vuex.store({
    state:{
        nickName:"张",
        firstName:"无极",
        todos:[{id:1,name:"1"},{id:2,name:"2"}]
    }
    getters:{
    	realName(state){
             return state.nickName+state.firstName
        }
        getTodoId:(state)=>(id)=>{
            return state.todos.find(todo=>todo.id === id)
        }
    }
})


/*在组件中使用 {{realName}} {{getTodoId(2)}} */
computed(){ //computed是不能传参数的
    realName(){
        return this.$store.getters.realName /* 通过属性访问*/
    },
    getTodoId(){ //不在此处接收传入的值
        return function(val){ //先return一个函数,把属性变成函数属性,参数接收传入的值
            return this.$store.getters.getTodoId(val) //再return出计算结果
        }
    }
}

```

使用 **mapGetters** 辅助函数 ，将store中得getter映射到局部计算属性

```js
import {mapGetters} from "vuex"
export default{
    //...
    computed:{
        ...mapGetters(["realName"])
    }
}
```



三、**Mutation** 更改Vuex的store中的状态的唯一提交方法是提交mutation。必须是同步函数

```js
const store = createStore({
    state:{
        count:1
    }
    mutations:{
        add(state,num){
   		    state.count+=num
        }
    }
})
```

在组件中使用，可以使用`this.$store.commit("xxx")`提交mutation,或者使用`mapMutations` 辅助函数；

```js
import { mapMutations } from "vuex"
export default({
    //...
    methods:{
        ...mapMutations ([
            'add', //将 `this.add(num)` 映射为 `this.$store.commit('add', num)`
        ]),
        add(){
            this.$store.commit("add",1)
        }
    }
})
```

四、Action  提交的是mutation,而不是直接变更状态；可以包含任意异步操作

```js
const store = createStore({
    state:{
        count:0
    }
    mutations:{
        add(state){
    		state.count++
        }
    },
    actions:{
        increment(context){
            setTimeOut(()=>{
                context.commit("add")
            },1000)
        }
    }
})
```

在组件中分发Actions,使用`this.$dispath("xxx")`分发，或者使用`mapActions`辅助函数将组件的`methods`映射为`store.dispath`调用

```js
import { mapActions } from "vuex"
export default{
    //...
    methods:{
        ...mapActions(["increment"])
    }
}
```



五、**Module** Vuex允许将store切割成模块 

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

