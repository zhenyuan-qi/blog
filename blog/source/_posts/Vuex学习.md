---
title: Vuex搞懂5大核心概念
date: 2022-03-21 18:02:38
tags: vue
---

#### Vuex是什么？

Vuex是全局状态管理器，集中式存储管理应用的所有组件状态。[官网解释](https://vuex.vuejs.org/zh/)

#### Vuex的核心概念

1. **state**单一状态树，用一个对象就包含了全部的应用层状态；

```js
import {createApp} from "vue"
import {createStore} from "vuex"
const store = createStore({
    state(){
        return{
            count:0
        }
    }
    mutations:{
    	add(state){
    		state.count++
		}
	}
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
        })
    }
}
```

当映射的计算属行名称与state的子节点名称一致，可以传入一个字符串数组

```js
computed:{
	...mapState(["count"])
}
```

