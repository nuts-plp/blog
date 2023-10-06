# solidity



































# vue

 



## swiper

##  axios

## vue-router

- 安装 `npm install vue-router`

  

## vuex

vuex有4个核心 state、getter、mutation、action

- 安装 `npm install vuex`

### state

- 创建一个store文件夹在src文件夹里，在store文件夹里创建index.js文件 内容如下

  ```js
  import { createStore } from 'vuex';
  //vuex的核心就是帮助我们管理组件之间的状态
  export default createStore({
      //所有的状态都放在这里
      state:{
          counter:0
      }
  })
  ```

  

- 在主文件中导入  main.js

```js
import './registerServiceWorker'
import store from'./store'
createApp(App).use(store).mount('#app')
```



- 用法一

```js
 <p>
    counter={{ $store.state.counter }}
  </p>
```

- 用法二

```js
<script>
import{ mapState } from 'vuex'
export default {
  name: 'App',
  
  computed: {
    ...mapState(["counter"])
  }
}
</script>
```



### getters

- 创建一个store文件夹在src文件夹里，在store文件夹里创建index.js文件 内容如下

  ```js
  import { createStore } from 'vuex';
  //vuex的核心就是帮助我们管理组件之间的状态
  export default createStore({
      //所有的状态都放在这里
      state:{
          counter:0
      },
      getters:{
          getCounter(state){
              return state.counter ? state.counter : "数据不合法"
          }
      }
  })
  ```

  

- 在主文件中导入  main.js

```js
import './registerServiceWorker'
import store from'./store'
createApp(App).use(store).mount('#app')
```



- 用法一

  ```js
  <p>getter={{ $store.getters.getCounter }}</p>
  ```

- 用法二

```js
<template>
 <p>getter={{ getCounter }}</p>
</template>

<script>
import { mapState,mapGetters } from 'vuex'
export default {
  name: 'App',
  computed:{
      ...mapGetters(["getCounter"])
  }
}
</script>
```





### moutation         同步修改状态

- 创建一个store文件夹在src文件夹里，在store文件夹里创建index.js文件 内容如下

  ```js
  import { createStore } from 'vuex';
  //vuex的核心就是帮助我们管理组件之间的状态
  export default createStore({
      //所有的状态都放在这里
      state:{
          counter:0
      },
      getters:{
          getCounter(state){
              return state.counter>10 ? state.counter:"数据有误！"
          },
          mutations:{
              addCounter(state,num){
                  state.counter+=num
              },
              dev(state){
                  state.counter--
              }
          }
      }
  })
  ```

  

- 在主文件中导入  main.js

```js
import './registerServiceWorker'
import store from'./store'
createApp(App).use(store).mount('#app')
```



- 方法一

```js
<template>
 <p>getter={{ getCounter }}</p>
 <button @click="addClickHandler">增加</button>
 <button @click="dev">减少</button>
</template>

<script>
import { mapGetters} from 'vuex'
export default {
  name: 'App',
  computed:{
      ...mapGetters(["getCounter"])
  },
  methods:{
    addClickHandler(){
       <--! 带参数调用--> 
      this.$store.commit("addCounter",15)
    },
      <--!不带参调用-->
    dev(){
      this.$store.commit("dev")
    }

  }
}
</script>
```

- 方法二

```js
<template>
 <p>getter={{ getCounter }}</p>
 <button @click="addClickHandler">增加</button>
 <button @click="dev">减少</button>
</template>

<script>
import { mapGetters} from 'vuex'
export default {
  name: 'App',
  computed:{
      ...mapGetters(["getCounter"])
  },
  methods:{
      ...mapMutation(["addCounter"]),
    addClickHandler(){
       <--! 带参数调用--> 
      this.addCounter(15)
    }
  }
}
</script>
```



### action

- 创建一个store文件夹在src文件夹里，在store文件夹里创建index.js文件 内容如下

  ```js
  import { createStore } from 'vuex';
  import axios from 'axios'
  //vuex的核心就是帮助我们管理组件之间的状态
  export default createStore({
      //所有的状态都放在这里
      state:{
          counter:15
      },
      getters:{
          getCounter(state){
              return state.counter>10 ? state.counter : "数据不符合要求!";
          }
      },
      mutations:{
          addCounter(state,num){
              state.counter +=num
          },
          dev(state){
              state.counter--
          }
      },
      //为异步操作所准备的
      actions:{
          asyncAddCounter({ commit }){
              axios.get("http://iwenwiki.com/api/generator/list.php")
              .then(res=>{
               commit("addCounter",res.data[0])  
              })
          }
      }
      
  })
  ```

  

- 在主文件中导入  main.js

```js
import './registerServiceWorker'
import store from'./store'
createApp(App).use(store).mount('#app')
```



- 方法一

```js
<template>
 <p>getter={{ getCounter }}</p>
 <button @click="addClickHandler">增加</button>
 <button @click="asyncAddCounterHandler">异步增加</button>
</template>

<script>

import { mapActions, mapGetters, mapMutations} from 'vuex'
export default {
  name: 'App',
  
  computed:{
      ...mapGetters(["getCounter"])
  },
  methods:{
   
    asyncAddCounterHandler(){
      this.$store.dispatch("asyncAddCounter")   
      // this.asyncAddCounter()
    }

  }
}
</script>
```

- 方法二

```js
<template>
 <p>getter={{ getCounter }}</p>
 <button @click="addClickHandler">增加</button>
 <button @click="asyncAddCounterHandler">异步增加</button>
</template>

<script>

import { mapActions, mapGetters, mapMutations} from 'vuex'
export default {
  name: 'App',
  
  computed:{
      ...mapGetters(["getCounter"])
  },
  methods:{
   ...mapActions(["asyncAddCounter"]),
    asyncAddCounterHandler(){ 
      this.asyncAddCounter()
    }

  }
}
</script>
```

