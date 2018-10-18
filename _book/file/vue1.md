# vue组件通信
组件间的通信是实际开发中非常常用的一环，如何使用对项目整体设计、开发、规范都有实际的作用，总结下vue组件间通信的几种方式，讨论下各自的使用场景

###文章对相关场景预览
---
- 父->子组件间的数据传递
- 子->父组件间的数据传递
- 兄弟组件间的数据传递
- 组件深层嵌套，祖先组件与子组件间的数据传递

## 父->子组件间的数据传递
---
父子组件的通信是开发最常用的也是最重要的，你们一定知道父子通信是用prop传递数据的，像这样：
```
// 父组件，传递数据
<editor :inputIndex="data" :inputName="aha"/>
```
```
// 子组件，接受数据，定义传递数据的类型type与默认值default
prop: {
	inputIndex: {
		type: Object,
		default: function () {
			return {}
			}
	},
	inputName: {
  	type: String,
  	default: ''
	}
}
```
#### 注意项：
1. 父组件传递数据时类似在标签中写了一个属性，如果是传递的数据时data中的自然是要在传递属性前加`v-bind:`，如果传递的是哟个已知的固定值呢
     - 字符串是静态的可直接传入无需在属性前加`v-bind`
     - 数字、布尔、对象、数组，因为这些是js表达式而不是字符串，所以即使这些传递的是静态的也是需要加`v-bind`，把数据放在data中引用
2. 如果prop传到子组件中的数据是一个对象的话，要注意传递的是一个对象要引用，虽然父子组件看似是分离的但最后都是在同一对象下
     - 如果prop传到子组件的值是作为初始值使用，且在父组件中不会变化赋值到**data**中使用
     - 如果传到子组件的prop的数据在父组件会被改变的，放到**计算属性**中监听变化使用。**因为如果传递的是个对象的话，只改变下面的某个属性子组件中是不会响应式更新的，如果子组件需要在数据变化时响应式更新那只能放到computed中或则watch深拷贝deep:true才能监听到变化**
     - 当然如果你又需要在子组件中通过prop传递数据的变化做些操作，那么写在computed中会报警告，因为计算属性中**不推荐有任何数据的改变**，最好只进行计算。如果你非要进行数据的操作那么可以把监听写在**watch**(注意deep深拷贝)或者使用computed的get和set如下：
     ```
     computed: {
        name: {
          get () {
            return `${this.firstName}`
          }
          set (name) {
            const names = name.split('')
            this.firstName = name[0]
          }
        }
     }
     ```
     - 但问题又来了，如果你传进来的是个对象，同时你又需要在子组件中操作传进来的这个数据，那么在父组件中的这个**数据也会改变，因为你传递的只是个引用**，即使你把prop的数据复制到**data**中也是一样的，无论如何赋值都是引用的赋值，**你只能对对象做深拷贝创建一个副本才能继续操作**，你可以用JSON的方法先转化字符串在转成对象更方便一点。
     - 所以在父子传递数据时要先考虑好数据要如何使用，否则你会遇到很多问题或子组件中修改了父组件中的数据，这是很隐蔽且很危险的

## 子->父组件间的数据传递
---
在vue中子向父传递数据一般用**$emit**自定义事件，在父组件中监听这个事件并在回调中写相关逻辑
```
// 父组件监听子组件定义的事件
<editor :inputIndex="index" @editorEmit="editorEmit"/>
```
```
// 子组件需要返回数据时执行，并可以传递数据
this.$emit('editorEmit', data)
```
那么问题来了，我是不是真的有必要去向父组件返回这个数据，用自定义事件可以再当子组件想传递数据或向子组件传递的数据有变化需要重新传递时执行，那么另外一种场景，父组件需要子组件的一个数据但子组件并不知道或者说没有能力在父组件想要的时候给父组件，，那么这个时候就要用到组件的一个选项ref:
`
<editor ref="editor" @editorEmit="editorEmit"/>
`
* 父组件在标签中定义ref属性，在js中直接调用**this.$refs.editor**就是调用整个子组件，子组件的所有内容都能通过ref去调用，当然我们并不推荐因为这会使数据看起来非常混乱
* **所以我们可以在子组件中定义一种专供父组件调用的函数**，比如我们在这个函数中返回子组件data中某个数据，当父组件想要获取这个数据就直接主动调用ref执行这个函数获取这个数据，这样能适应很大一部分场景，逻辑也更清晰一点。
* 另外，父向子传递数据也可以用ref，有次需要在一个父组件中大量调用同一个子组件，而每次调用传递的prop数据都不同，并且传递数据会根据之后操作变化，这样我需要在data中定义大量相关数据并改变它，我可以**直接用ref调用子组件函数直接把数据以参数的形式传给子组件**，逻辑一下子清晰了
* 如果调用基础组件可以在父组件中调用ref执行基础组件中暴露的各种功能接口，比如显示，消失等
## 兄弟组件间的数据传递
---
vue中兄弟组件间的通信是很不方便的，或者说不支持的，那么父子组件中都有什么通信方式呢
* **路由URL参数**
    - 在传统开发时我们常常把需要跨页面传递的数据放到url后面，跳转到另外页面时直接获取url字符串获取想要的参数即可，在vue跨组件时一样可以这么做，
    ```
    // router index.js 动态路由
    {
      path:'/params/:Id',
      component:Params,
      name：Params
    }
    ```
    ```
    // 跳转路由
    <router-link :to="/params/12">跳转路由</router-link>
    ```
    - 在跳转后的组件中用$route.params.id去获取到这个id参数为12，但这种只适合传递比较小的数据，数字之类的
* **Bus通信**
在组件之外定义一个bus.js作为组件间通信的桥梁，适用于比较小型不需要vuex又需要兄弟组件通信的
  1. bus.js中添加如下
  ```
  import Vue from 'vue'
  export default new Vue
  ```
  2. 组件中调用bus.js通过自定义事件传递数据
  ```
  import Bus from './bus.js'
  export default {
      methods: {
         bus () {
            Bus.$emit('msg', '我要传给兄弟组件们')
         }
      }
  }
  ```
  3. 兄弟组件中监听事件接受数据
  ```
  import Bus from './bus.js'
    export default {
      mounted() {
        Bus.$on('msg', (e) => {
          console.log(e)
          })
      }
    }
  ```
  注：以上两种使用场景并不高所以只是简略提一下
* **Vuex集中状态管理**vuex是vue的集中状态管理工具，对于大型应用统一集中管理数据，很方便，在此对vuex的用法并不过多介绍只是提一下使用过程中遇到的问题
    - 规范：对于多人开发的大型应用规范的制定是至关重要的，对于所有人都会接触到的vuex对其修改数据调用数据都应有一个明确严格的使用规范
      1. vuex分模块：项目不同模块间维护各自的vuex数据
      2. 限制调用：只允许action操作数据，getters获取数据，使用mapGetters，mapActions辅助函数调用数据
![图片alt](https://user-gold-cdn.xitu.io/2018/10/9/166589feba1497e0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
    - 对于vuex的使用场景也有一些争论，有人认为正常组件之间就是要用父子组件传值的方式，即使子组件需要使vuex中的数据也应该由父组件获取再传到子组件中，但有的时候组件间嵌套很深，只允许父组件获取数据并不是一个方便的方法，所以对于祖先元组件与子组件传值又有了新问题，vue官网也有一些方法解决，如下

## **祖先组件与子组件间的数据传递**
---
[provide/inject](https://cn.vuejs.org/v2/api/#provide-inject) 除了正常的父子组件传值外，vue也提供了provide/inject
>这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效

官网实例
```
// 父级组件提供 'foo'
var Provider = {
  provide: {
    foo: 'bar'
  },
  // ...
}

// 子组件注入 'foo'
var Child = {
  inject: ['foo'],
  created () {
    console.log(this.foo) // => "bar"
  }
}

```
* provide 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。
* 一个字符串数组，或 一个对象，对象的 key 是本地的绑定名，value 是：
  - 在可用的注入内容中搜索用的 key (字符串或 Symbol)，或 一个对象，该对象的：
    * from 属性是在可用的注入内容中搜索用的 key (字符串或 Symbol)
    * default 属性是降级情况下使用的 value

>提示：provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。

## $attrs/inheritAttrs
---
**场景**：祖先组件与子组件传值
  * 如果是props的话，就必须在子组件与祖先组件之间每个组件都要prop接受这个数据，再传到下一层子组件，这就很麻烦，耦合深程序臃肿
  * 如果用vuex确实显得有点小题大做了，所以用$attrs直接去获取祖先数据也不错

>包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。

以上是官网对$attrs的解释，我刚看我也是一脸懵逼，回去试了一下其实并不难，而且比较适用组件深层嵌套场景下，**祖先组件向子组件传值的问题**

**使用：**

祖先组件
```
// 祖先组件
// 在祖先组件中直接传入output和input
<template>
  <div>
    <child1 :output='output' :input="input"></child1>
  </div>
</template>
<script>
import child1 from './child1.vue'
export default {
  components: {
    child1
  },
  data () {
    return {
      input: 'jijijijjijiji',
      output: {
        name: 'aha',
        age: '18'
      }
    }
  }
</script>
```
子组件
```
<template>
  <div
    <h1>{{input}}</h1>
    <child2 :child="child" v-bind='$attrs'></child2>
  </div>
</template>
<script>
import child2 from './child2.vue'
export default {
  components: {
    child2
  },
  props: {
    input: [String]
  },
  data () {
    return {
      child: 'child1child1child1child1s'
    }
  },
// 默认为true，如果传入的属性子组件没有prop接受，就会以字符串的形式出现为标签属性
// 设为false，在dom中就看不到这些属性，试一下就知道了
  inheritAttrs: false,
  created () {
    // 在子组件中打印的$attrs就是父组件传入的值，刨去style,class,和子组件中已props的属性
    console.log(this.$attrs)  // 打印output
  }
}
</script>
```
孙组件
```
<template>
  <div>
    {{$attrs.output.name}}
  </div>
</template>
<script>
export default {
  created () {
    // 打印output和child
    console.log(this.$attrs)
  }
}
</script>
```

### **$children/$parent**
* 当然你可以直接用$children/$parent获取当前组件的子组件实例或父组件实例（如果有的话），也能对其做些操作，不过并不推荐这么做
* 你还可以放到localStorage，sessionStorage，cooikes之类的存在本地当然也能做到组件间的通信