# 一. 事件处理

## 1. 常用事件

> 冒泡：如果子元素和父级元素触发的是相同事件的时候，当子元素被触发的时候父元素也会被触发冒泡机制，这就是冒泡			的基本原理。
>

```
@keydown	按下任意按键
@keyup		释放任意按键
@click		在元素上 按下并释放任意鼠标按键
@dblclick	在元素上 双击鼠标事件

@focus		元素获得焦点（不会冒泡）
@blur		元素失去焦点（不会冒泡）

@wheel		滚轮向任意方向移动
@submit		点击提交按钮
@reset		点击重置按钮
```

## 2. 事件修饰符

```
prevent	阻止默认事件
stop	阻止事件冒泡
once	事件只触发一次
passive	这个我们以移动端监听元素滚动事件说明，在监听元素滚动事件的时候，会一直触发onscroll事件让页面变的越来越卡，因		  此在我们使用这个修饰符后，相当于给onscroll事件增加了.lazy修饰符
```

## 3. 表单修饰符

```
v-model.lazy=''		当光标离开时才会修改vue中value值
trim				去掉头尾空格
number				将输入的值转换为数值类型
```

## 4. 鼠标按钮修饰符

```
@click.left		鼠标左键
@click.right	鼠标右键
@click.middle	鼠标中建
```

## 5. 键盘修饰符

```
键盘修饰符用来修饰键盘的事件（如：onkeyup、onkeydown等），键盘的事件触发需要相对应的keyCode。然而keyCode存在很多，因此为了使用方便Vue给我们提供了别名的修饰符，分为以下两种:

普通按键（enter、delete、space、tab、esc…）
系统修饰键（ctrl、shift、alt…）也可以直接用按键的代码来做修饰符（如：enter为13）
```



# 二. 计算属性/侦听属性

## 1. 计算属性

```
computed: {
      //完整写法
      // fullName: {
      // 	get() {
      // 		console.log('get被调用了')
      // 		return this.firstName + '-' + this.lastName
      // 	},
      // 	set(value) {
      // 		console.log('set', value)
      // 		const arr = value.split('-')
      // 		this.firstName = arr[0]
      // 		this.lastName = arr[1]
      // 	}
      // }

      // 简写
      fullName() {
        console.log('get被调用了')
        return this.firstName + '-' + this.lastName
      }
    }
```

## 2. 侦听属性



深度监听

1. Vue中的watch默认不监测对象内部值的改变（一层）
2. 在watch中配置deep:true可以监测对象内部值的改变（多层）

注意：

1. Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以
2. 使用watch时根据监视数据的具体结构，决定是否采用深度监视





写法：

```
    watch: {
      // 正常写法
      // isHot: {
      // 	// immediate:true, //初始化时让handler调用一下
      // 	// deep:true,	//深度监视
      // 	handler(newValue, oldValue) {
      // 		console.log('isHot被修改了', newValue, oldValue)
      // 	}
      // },

      //简写
      isHot(newValue, oldValue) {
        console.log('isHot被修改了', newValue, oldValue, this)
      }
    }
  })

  //正常写法
  // vm.$watch('isHot', {
  // 	immediate: true, //初始化时让handler调用一下
  // 	deep: true,//深度监视
  // 	handler(newValue, oldValue) {
  // 		console.log('isHot被修改了', newValue, oldValue)
  // 	}
  // })l

  //简写
  // vm.$watch('isHot', (newValue, oldValue) => {
  // 	console.log('isHot被修改了', newValue, oldValue, this)
  // })
```



# 三. 绑定样式

## 1. class样式

```
class样式
●
写法：:class="xxx"，xxx 可以是字符串、数组、对象
●
:style="[a,b]"其中a、b是样式对象
●
:style="{fontSize: xxx}"其中 xxx 是动态值
○
字符串写法适用于：类名不确定，要动态获取 
○
数组写法适用于：要绑定多个样式，个数不确定，名字也不确定 
○
对象写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用 


代码中，绑定 class 样式三个示例：
1. 字符串写法：点击按钮、随机切换心情，根据心情变换样式
2. 数组写法：例如通过数组的 push 和 pop 方法，动态的向数组中添加 / 删除样式名，就可以修改样式
3. 对象写法：修改对象中的样式名对应的值（true / false），决定是否添加该样式
```

## 2. 条件渲染

```
v-if
●
 写法 跟 if else 语法类似
v-if="表达式"
v-else-if="表达式"
v-else
●
 适用于：切换频率较低的场景，因为不展示的DOM元素直接被移除
●
 注意：v-if可以和v-else-ifv-else一起使用，但要求结构不能被打断
v-show
●
写法：v-show="表达式"
●
适用于：切换频率较高的场景
●
特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉display: none
备注：	使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到
	template标签不影响结构，页面html中不会有此标签，但只能配合v-if，不能配合v-show
```

# 四. 列表渲染

## 1. v-for

```
v-for指令
●
用于展示列表数据
●
语法：<li v-for="(item, index) of items" :key="index">，这里key可以是index，更好的是遍历对象的唯一标识
●
可遍历：数组、对象、字符串（用的少）、指定次数（用的少）
```

```
开发中如何选择key？
a. 最好使用每条数据的唯一标识作为key，比如 id、手机号、身份证号、学号等唯一值
b. 如果不存在对数据的逆序添加、逆序删除等破坏顺序的操作，仅用于渲染列表，使用index作为key是没有问题的
```

## 2. 搜索栏-过滤实例

```
<title>列表过滤</title>
<script type="text/javascript" src="../js/vue.js"></script>

<div id="root">
  <h2>人员列表</h2>
  <input type="text" placeholder="请输入名字" v-model="keyWord">
  <ul>
    <li v-for="(p,index) of filPersons" :key="p.id">
      {{ p.name }}-{{ p.age }}-{{ p.sex }}
    </li>
  </ul>
</div>

<script type="text/javascript">
  Vue.config.productionTip = false
  // 用 watch 实现
  // #region 
  /* new Vue({
			el: '#root',
			data: {
				keyWord: '',
				persons: [
					{ id: '001', name: '马冬梅', age: 19, sex: '女' },
					{ id: '002', name: '周冬雨', age: 20, sex: '女' },
					{ id: '003', name: '周杰伦', age: 21, sex: '男' },
					{ id: '004', name: '温兆伦', age: 22, sex: '男' }
				],
				filPersons: []
			},
			watch: {
				keyWord: {
					immediate: true,
					handler(val) {
						this.filPersons = this.persons.filter((p) => {
							return p.name.indexOf(val) !== -1
						})
					}
				}
			}
		}) */
  //#endregion

  // 用 computed 实现
  new Vue({
    el: '#root',
    data: {
      keyWord: '',
      persons: [
        { id: '001', name: '马冬梅', age: 19, sex: '女' },
        { id: '002', name: '周冬雨', age: 20, sex: '女' },
        { id: '003', name: '周杰伦', age: 21, sex: '男' },
        { id: '004', name: '温兆伦', age: 22, sex: '男' }
      ]
    },
    computed: {
      filPersons() {
        return this.persons.filter((p) => {
          return p.name.indexOf(this.keyWord) !== -1
        })
      }
    }
  }) 
</script>
```

# 五. 收集表单数据

```
收集表单数据
●
若<input type="text"/>，则v-model收集的是value值，用户输入的内容就是value值
●
若<input type="radio"/>，则v-model收集的是value值，且要给标签配置value属性
●
若<input type="checkbox"/> 
○
没有配置value属性，那么收集的是checked属性（勾选 or 未勾选，是布尔值）
○
配置了value属性
■
v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
■
v-model的初始值是数组，那么收集的就是value组成的数组
v-model的三个修饰符
a.lazy		失去焦点后再收集数据
b. number	输入字符串转为有效的数字
c. trim		输入首尾空格过滤
```

# 六. 全局自定义指令

写法

```
Vue.directive(指令名, 配置对象)
或
Vue.directive(指令名, 回调函数)


Vue.directive('fbind', {
    // 指令与元素成功绑定时（一上来）
    bind(element, binding) {	// element就是DOM元素，binding就是要绑定的
      element.value = binding.value
    },
    // 指令所在元素被插入页面时
    inserted(element, binding) {
      element.focus()
    },
    // 指令所在的模板被重新解析时
    update(element, binding) {
      element.value = binding.value
    }
})
```

注意：

```
配置对象中常用的3个回调函数 
bind(element, binding)	 指令与元素成功绑定时调用
inserted(element, binding)指令所在元素被插入页面时调用
update(element, binding)	 指令所在模板结构被重新解析时调用
element就是DOM元素，binding就是要绑定的对象，它包含以下属性：namevalueoldValueexpressionargmodifiers

备注：
a. 指令定义时不加v-，但使用时要加v-
b. 指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名
```

例子：

```
<div id="root">
  <h2>{{ name }}</h2>
  <h2>当前的n值是：<span v-text="n"></span> </h2>
  <!-- <h2>放大10倍后的n值是：<span v-big-number="n"></span> </h2> -->
  <h2>放大10倍后的n值是：<span v-big="n"></span> </h2>
  <button @click="n++">点我n+1</button>
  <hr />
  <input type="text" v-fbind:value="n">
</div>

<script type="text/javascript">
  Vue.config.productionTip = false

  // 定义全局指令
  /* Vue.directive('fbind',{
		// 指令与元素成功绑定时（一上来）
		bind(element,binding){
			element.value = binding.value
		},
		// 指令所在元素被插入页面时
		inserted(element,binding){
			element.focus()
		},
		// 指令所在的模板被重新解析时
		update(element,binding){
			element.value = binding.value
		}
	}) */

  new Vue({
    el: '#root',
    data: {
      name: '尚硅谷',
      n: 1
    },
    directives: {
      // big函数何时会被调用？
      // 1.指令与元素成功绑定时（一上来） 2.指令所在的模板被重新解析时
      /* 'big-number'(element,binding){
				// console.log('big')
				element.innerText = binding.value * 10
			}, */
      big(element, binding) {
        console.log('big', this) // 🔴注意此处的 this 是 window
        // console.log('big')
        element.innerText = binding.value * 10
      },
      fbind: {
        // 指令与元素成功绑定时（一上来）
        bind(element, binding) {
          element.value = binding.value
        },
        // 指令所在元素被插入页面时
        inserted(element, binding) {
          element.focus()
        },
        // 指令所在的模板被重新解析时
        update(element, binding) {
          element.value = binding.value
        }
      }
    }
  })
</script>
```





# 七. 脚手架配置

```
3.1. 初始化脚手架

3.1.1. 说明
1. Vue脚手架是Vue官方提供的标准化开发工具（开发平台）
2. 最新的版本是 4.x
3. 文档 Vue CLI

3.1.2. 具体步骤
1. 如果下载缓慢请配置npm淘宝镜像npm config set registry http://registry.npm.taobao.org
2. 全局安装 @vue/cli npm install -g @vue/cli
3. 切换到创建项目的目录，使用命令创建项目vue create xxx
4. 选择使用vue的版本
5. 启动项目npm run serve
6. 打包项目npm run build
7. 暂停项目 Ctrl+C
```

> Vue脚手架隐藏了所有webpack相关的配置，若想查看具体的webpack配置，请执行
> vue inspect > output.js

```
module.exports = {
  pages: {
    index: {
      entry: 'src/index/main.js' // 入口
    }
  },
  lineOnSave: false	// 关闭语法检查
}
```

# 八. 属性

## 1. ref

```
ref被用来给元素或子组件注册引用信息（id的替代者）
●应用在html标签上获取的是真实DOM元素，应用在组件标签上获取的是组件实例对象vc
●使用方式 
a.打标识：<h1 ref="xxx"></h1>或<School ref="xxx"></School>
b.获取：this.$refs.xxx
```

```
<template>
  <div>
    <h1 v-text="msg" ref="title"></h1>
    <button ref="btn" @click="showDOM">点我输出上方的DOM元素</button>
    <School ref="sch"/>
  </div>
</template>

<script>
  import School from './components/School'

  export default {
    name:'App',
    components:{ School },
    data() {
      return {
        msg:'欢迎学习Vue！'
      }
    },
    methods: {
      showDOM(){
        console.log(this.$refs.title)	// 真实DOM元素
        console.log(this.$refs.btn)		// 真实DOM元素
        console.log(this.$refs.sch)		// School组件的实例对象（vc）
      }
    },
  }
</script>
```



## 2. props配置项

```
props让组件接收外部传过来的数据 
●传递数据<Demo name="xxx" :age="18"/>这里age前加:，通过v-bind使得里面的18是数字
●接收数据
 第一种方式（只接收）props:['name', 'age'] 
 第二种方式（限制类型）props:{name:String, age:Number}
 第三种方式（限制类型、限制必要性、指定默认值） 
```

> 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中，然后去修改data中的数据

实例：

```
<template>
  <div>
    <Student name="李四" sex="女" :age="18"/>
    <Student name="王五" sex="男" :age="18"/>
  </div>
</template>

<script>
  import Student from './components/Student'

  export default {
    name:'App',
    components:{ Student }
  }
</script>
```

```
<template>
  <div>
    <h1>{{ msg }}</h1>
    <h2>学生姓名：{{ name }}</h2>
    <h2>学生性别：{{ sex }}</h2>
    <h2>学生年龄：{{ myAge + 1 }}</h2>
    <button @click="updateAge">尝试修改收到的年龄</button>
  </div>
</template>

<script>
export default {
  name: "Student",
  data() {
    console.log(this);
    return {
      msg: "我是一个UESTC大学的学生",
      myAge: this.age,
    };
  },
  methods: { updateAge() { this.myAge++; }, },
  // 简单声明接收
  // props:['name','age','sex']

  // 接收的同时对数据进行类型限制
  //   props: {
  //     name: String,
  //     age: Number,
  //     sex: String,
  //   }

  // 接收的同时对数据：进行类型限制+默认值的指定+必要性的限制
  props: {
    name: {
      type: String, 	//name的类型是字符串
      required: true, //name是必要的
    },
    age: {
      type: Number,
      default: 99, //默认值
    },
    sex: {
      type: String,
      required: true,
    },
  },
};
</script>
```

## 3. mixin混入（不常用，略）

## 4. plugins插件

用于各类插件的引用，lue

## 5. scoped

让样式局部生效（默认模板）



# 九. 本地存储

```
存储内容大小一般支持 5MB 左右（不同浏览器可能还不一样） 
浏览器端通过Window.sessionStorage和Window.localStorage属性来实现本地存储机制

相关API
xxxStorage.setItem('key', 'value')该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值
xxxStorage.getItem('key')该方法接受一个键名作为参数，返回键名对应的值
xxxStorage.removeItem('key')该方法接受一个键名作为参数，并把该键名从存储中删除
xxxStorage.clear()该方法会清空存储中的所有数据
备注
○SessionStorage存储的内容会随着浏览器窗口关闭而消失
○LocalStorage存储的内容，需要手动清除才会消失
○xxxStorage.getItem(xxx)如果 xxx 对应的 value 获取不到，那么getItem()的返回值是null
○JSON.parse(null)的结果依然是null
```

localStorage

```
<h2>localStorage</h2>
<button onclick="saveDate()">点我保存数据</button><br/>
<button onclick="readDate()">点我读数据</button><br/>
<button onclick="deleteDate()">点我删除数据</button><br/>
<button onclick="deleteAllDate()">点我清空数据</button><br/>

<script>
  let person = {name:"JOJO",age:20}

  function saveDate(){
    localStorage.setItem('msg','localStorage')
    localStorage.setItem('person',JSON.stringify(person))
  }
  function readDate(){
    console.log(localStorage.getItem('msg'))
    const person = localStorage.getItem('person')
    console.log(JSON.parse(person))
  }
  function deleteDate(){
    localStorage.removeItem('msg')
    localStorage.removeItem('person')
  }
  function deleteAllDate(){
    localStorage.clear()
  }
</script>
```

sessionStorage

```
<h2>sessionStorage</h2>
<button onclick="saveDate()">点我保存数据</button><br/>
<button onclick="readDate()">点我读数据</button><br/>
<button onclick="deleteDate()">点我删除数据</button><br/>
<button onclick="deleteAllDate()">点我清空数据</button><br/>

<script>
  let person = {name:"JOJO",age:20}

  function saveDate(){
    sessionStorage.setItem('msg','sessionStorage')
    sessionStorage.setItem('person',JSON.stringify(person))
  }
  function readDate(){
    console.log(sessionStorage.getItem('msg'))
    const person = sessionStorage.getItem('person')
    console.log(JSON.parse(person))
  }
  function deleteDate(){
    sessionStorage.removeItem('msg')
    sessionStorage.removeItem('person')
  }
  function deleteAllDate(){
    sessionStorage.clear()
  }
</script>
```





# 十. 全局事件总线

```
一种可以在任意组件间通信的方式，本质上就是一个对象，它必须满足以下条件
1. 所有的组件对象都必须能看见他 
2. 这个对象必须能够使用$on$emit$off方法去绑定、触发和解绑事件
```



主要用于实现子传父情况：

在父组件中定义事件，在子组件中设计方法调用事件进行传值



使用步骤：

1. 定义

   ```
   new Vue({
      	...
      	beforeCreate() {
      		Vue.prototype.$bus = this // 安装全局事件总线，$bus 就是当前应用的 vm
      	},
       ...
   })
   ```

2. 父组件定义事件

   ```
   mounted() {
       this.$bus.$on('demo',(data) => {
         console.log('收到了信息',data)
       })
     },
   beforeDestroy() {
     	this.$bus.$off('demo');
     }
   ```

3. 子组件调用事件

   ```
   methods: {
       demo(){
         this.$bus.$emit('demo',this.name);
       }
   }
   ```





# 十一. Vuex

## 1. 搭建环境

1. 下载安装 npm i vuex

   > ps：若使用vue2，下载为 npm i vuex@3

2. 创建src/store/index.js该文件用于创建Vuex中最为核心的store

```
import Vue from 'vue'
import Vuex from 'vuex'	// 引入Vuex

Vue.use(Vuex)	// 应用Vuex插件

const actions = {}		// 准备actions——用于响应组件中的动作
const mutations = {}	// 准备mutations——用于操作数据（state）
const state = {}			// 准备state——用于存储数据

// 创建并暴露store
export default new Vuex.Store({
	actions,
	mutations,
	state,
})
```

3. 在src/main.js中创建vm时传入store配置项

   ```
   import Vue from 'vue'
   import App from './App.vue'
   import store from './store'	// 引入store
   
   Vue.config.productionTip = false
   
   new Vue({
   	el: '#app',
   	render: h => h(App),
   	store,										// 配置项添加store
   	beforeCreate() {
   		Vue.prototype.$bus = this
   	}
   })
   ```

## 2. 基本使用

```
Vuex的基本使用
1.初始化数据state，配置actions、mutations，操作文件store.js
2.组件中读取vuex中的数据$store.state.数据
3.组件中修改vuex中的数据$store.dispatch('action中的方法名',数据) 
  或$store.commit('mutations中的方法名',数据)  
  若没有网络请求或其他业务逻辑，组件中也可越过actions，即不写dispatch，直接编写commit
```

## 3. getter配置项

概念：

1. 当state中的数据需要经过加工后再使用时，可以使用getters加工，相当于全局计算属性
2. 在store.js中追加getters配置

```
const getters = {
	bigSum(state){
		return state.sum * 10
	}
}

// 创建并暴露store
export default new Vuex.Store({
	......
	getters
})
```

3. 组件中读取数据$store.getters.bigSum

## 4. map方法的使用

```
computed: {
  	// 借助mapState生成计算属性：sum、school、subject（对象写法一）
  	...mapState({sum:'sum',school:'school',subject:'subject'}),

  	// 借助mapState生成计算属性：sum、school、subject（数组写法二）
  	...mapState(['sum','school','subject']),
},
```

## 5. 模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确

2. 修改store.js
   为了解决不同模块命名冲突的问题，将不同模块的namespaced: true，之后在不同页面中引入getteractionsmutations时，需要加上所属的模块名

   ```
   const countAbout = {
     namespaced: true,	// 开启命名空间
     state: {x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){ return state.sum * 10 }
     }
   }
   
   const personAbout = {
     namespaced: true,	// 开启命名空间
     state: { ... },
     mutations: { ... },
     actions: { ... }
   }
   
   const store = new Vuex.Store({
     modules: {
       countAbout,
       personAbout
     }
   })
   ```

3.  开启命名空间后，组件中读取state数据

   ```
   // 方式一：自己直接读取
   this.$store.state.personAbout.list
   // 方式二：借助mapState读取：
   ...mapState('countAbout',['sum','school','subject']),
   ```

4. 开启命名空间后，组件中读取getters数据

   ```
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取：
   ...mapGetters('countAbout',['bigSum'])
   ```

5.  开启命名空间后，组件中调用dispatch

   ```
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person)
   //方式二：借助mapActions：
   ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   ```

6. 开启命名空间后，组件中调用commit

   ```
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations：
   ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
   ```

   

# 十二. 路由

## 1. 基本配置

1. 安装vue-router，命令npm i vue-router

   > ps：使用vue2时，npm i vue-router@3

3. 在router/index.js下，编写router配置项

   ```
   import VueRouter from 'vue-router'			// 引入VueRouter
   import About from '../components/About'	// 路由组件
   import Home from '../components/Home'		// 路由组件
   
   // 创建router实例对象，去管理一组一组的路由规则
   const router = new VueRouter({
   	routes:[
   		{
   			path:'/about',
   			component:About
   		},
   		{
   			path:'/home',
   			component:Home
   		}
   	]
   })
   
   //暴露router
   export default router
   ```

3. 在main.js下添加

   ```
   import Vue from 'vue'
   import App from './App.vue'
   //	***
   import VueRouter from "vue-router";
   import router from "@/router";
   
   Vue.config.productionTip = false
   //	***
   Vue.use(VueRouter)
   
   new Vue({
     render: h => h(App),
     router	//	***
   }).$mount('#app')
   ```

4. 指定展示位<router-view></router-view>

## 2. 注意事项

1. 路由组件通常存放在pages文件夹，一般组件通常存放在components文件夹 比如上一节的案例就可以修改为 src/pages/Home.vue src/pages/About.vue src/router/index.js src/components/Banner.vue src/App.vue
2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载
3. 每个组件都有自己的$route属性，里面存储着自己的路由信息
4. 整个应用只有一个router，可以通过组件的$router属性获取到



## 3. 多级路由

1. 配置路由规则，使用children配置项

   ```
   routes:[
   	{
   		path:'/about',
   		component:About,
   	},
   	{
   		path:'/home',
   		component:Home,
   		children:[ 					// 通过children配置子级路由
   			{
   				path:'news', 		// 此处一定不要带斜杠，写成 /news
   				component:News
   			},
   			{
   				path:'message',	// 此处一定不要写成 /message
   				component:Message
   			}
   		]
   	}
   ]
   ```

2. 跳转

   ```
   <router-link to="/home/news">News</router-link>
   ```



## 4. 编程式路由导航

作用：不借助<router-link>实现路由跳转，让路由跳转更加灵活

this.$router.push({})	内传的对象与<router-link>中的to相同

this.$router.replace({})	

this.$router.forward()	前进

this.$router.back()		后退

this.$router.go(n)		可前进也可后退，n为正数前进n，为负数后退

```
this.$router.push({
	name:'xiangqing',
  params:{
    id:xxx,
    title:xxx
  }
})

this.$router.replace({
	name:'xiangqing',
  params:{
    id:xxx,
    title:xxx
  }
})
```

## 5. 缓存路由组件

```
作用：让不展示的路由组件保持挂载，不被销毁
<keep-alive include="News"><router-view></router-view></keep-alive>
<keep-alive :include="['News', 'Message']"><router-view></router-view></keep-alive>
```

```
// 缓存一个路由组件
<keep-alive include="News"> // include中写想要缓存的组件名，不写表示全部缓存
    <router-view></router-view>
</keep-alive>

// 缓存多个路由组件
<keep-alive :include="['News','Message']"> 
    <router-view></router-view>
</keep-alive>
```

## 6. 路由守卫（略）

​	暂用不上



# 十三. elementUI组件库

1. 安装 element-ui：npm i element-ui -S 

2. src/main.js

   ```
   import Vue from 'vue'
   import App from './App.vue'
   import ElementUI from 'element-ui';							// 引入ElementUI组件库
   import 'element-ui/lib/theme-chalk/index.css';	// 引入ElementUI全部样式
   
   Vue.config.productionTip = false
   
   Vue.use(ElementUI)	// 使用ElementUI
   
   new Vue({
       el:"#app",
       render: h => h(App),
   })
   ```

> ps：按需引入方法暂略





# 十四. axios

安装axios

```
npm i axios@3
```



## 1. 开启服务端口

1. 安装express

2. 开启端口

   ```
   const express = require('express');
   const app = express();
   
   app.get('/data',(request,response)=>{
       const data = [
           {
               name: '胡海洋',
               age: 20
           },
           {
               name: '夕',
               age: 18
           }
       ]
       response.send(data);
   })
   
   app.listen(8000,()=>{
       console.log('8000端口监听中');
   })
   ```

3. 运行端口

   ```
   node demo01.js
   ```



## 2. 配置文件

```
devServer: {
        proxy: {
            '/api1': {
                target: 'http://localhost:5000',
                pathRewrite:{'^/api1':''},
                // ws: true, //用于支持websocket,默认值为true
                // changeOrigin: true //用于控制请求头中的host值,默认值为true
            },
            '/api2': {
                target: 'http://localhost:5001',
                pathRewrite:{'^/api2':''},
            }
        }
    }
```

```
  methods :{
    get(){
      axios.get('http://localhost:8080/api/data').then(
          response => {
            console.log('success',response.data);
            this.tableData=response.data;
          },
          error => {
            console.log('error',error.message)
          }
      )
    }
  }
```

## 3. 全局挂载axios

```
Vue.prototype.$axios = axios; //全局挂载axios

new Vue({
  render: h => h(App),
  store,
  router,
  axios,
  beforeCreate() {
    /*Vue.prototype.$bus = this;*/
  }

}).$mount('#app')
```



# 十五. 插槽

> <slot>插槽：让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式，
>       		适用于 父组件 ===> 子组件

暂略