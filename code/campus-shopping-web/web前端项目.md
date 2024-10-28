```ad-info
api文档：https://apifox.com/apidoc/project-4286520
```

# ♾️项目搭建
1. 拉取vue3框架
```shell
pnpm create vite project
cd project
pnpm i
```
2. main.js
```ts
import {createApp} from 'vue'
import App from './App.vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import router from "./router/index.js";
import {createPinia} from "pinia";
const pinia = createPinia()
const app = createApp(App)
app.use(ElementPlus)
    .use(router)
    .use(pinia)
app.mount('#app')
```
3. `vite-env.d.ts`
```ts
/// <reference types="vite/client" />  
//解决ts文件引入vue文件出现红色警告问题  
declare module '*.vue' {  
import { defineComponent } from 'vue'  
const Component: ReturnType<typeof defineComponent>  
export default Component  
}
```

# ♾️路由
0. 拉取router
```shell
pnpm install vue-router@4
```
1. `router/index.ts`
```ts
import { createRouter, createWebHashHistory } from 'vue-router'  
import { constantRoutes } from './routes'  
	// 创建路由器  
	const router = createRouter({  
	// 路由模式  
	history: createWebHashHistory(),  
	// 路由地址  
	routes: constantRoutes,  
	// 滚动行为  
	scrollBehavior: () => ({  
	top: 0,  
	left: 0,  
	}),  
})  
  
export default router
```
2. `router/route.ts`
```ts
export const constantRoutes = [  
	{  
	path: '/login',  
	component: () => import('@/views/login/index.vue'),  
	name: 'login',  
	},  
	{  
	path: '/',  
	component: () => import('@/views/home/index.vue'),  
	name: 'layout',  
	},  
	{  
	path: '/404',  
	component: () => import('@/views/404/index.vue'),  
	name: '404',  
	},  
	{  
	// 匹配所有路径  
	path: '/:pathMatch(.*)*',  
	redirect: '/404',  
	name: 'Any',  
	},  
]
```

3. `main.ts`
```ts
import {createApp} from 'vue'  
import App from './App.vue'  
import router from './router/index' // 引入路由  
  
const app = createApp(App)  
app.use(router)  
  
app.mount('#app')
```

# ♾️axios
1. 下载
```shell
pnpm install axios
```
2. `util.request.ts`
```ts
// 进行axios的二次封装  
import axios from 'axios'  
// 第一步：利用axios.create创建一个axios实例  
const request = axios.create({  
baseURL: "http://111.229.78.126:9080", // 基础路径,通过环境变量获取  
timeout: 5000, // 超时时间  
})  
// 第二步：请求拦截器  
request.interceptors.request.use((config) => {  
// config 本次请求的配置对象，可以修改  
return config  
})  
// 第三步：响应拦截器  
request.interceptors.response.use(  
(response) => {  
// response 响应数据  
return response.data  
},  
(error) => {  
console.log('请求出错：', error)  
},  
)  
  
// 第四步：导出request  
export default request
```

# ♾️element-plus
1. 下载
```shell
pnpm i element-plus
```
2. **index.html**
```ts
<!-- Import style -->
<link href="//unpkg.com/element-plus/dist/index.css" rel="stylesheet"/>
```

