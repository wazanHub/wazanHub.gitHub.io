---
title: "Vue知识点"
date: 2020-06-30T13:59:25+08:00
draft: false
categories: ["学习笔记"]
tags: ["vue"]
---

## Vue.use()
源码：
```
Vue.use = function (plugin: Function | Object) {
  //Vue构造函数上定义_installedPlugins 避免相同的插件注册多次
  const installedPlugins = (this._installedPlugins || (this._installedPlugins = []))
  // import是单例模式
  //所以plugin不论是Fuction还是Object同一个插件都是同一个
  if (installedPlugins.indexOf(plugin) > -1) {
   return this
  }
 
  // additional parameters
  const args = toArray(arguments, 1)
  // Vue作为第一个参数传递给插件
  args.unshift(this)
  if (typeof plugin.install === 'function') {
   plugin.install.apply(plugin, args)
  } else if (typeof plugin === 'function') {
   plugin.apply(null, args)
  }
  installedPlugins.push(plugin)
  return this // 返回的是this,可以链式调用
 }
```
通常在组件内使用一个插件都会先
```
import Vue from 'vue'
import Element from 'element-ui'
Vue.use(Element)  
new Vue({})
```

为什么要使用Vue.use(组件)？  
因为引入一个组件首先要初始化插件，而为Vue定制的插件里都会暴露一个 `install` 方法  
Vue.use()就是要运行这个 `install`
演示如何用Vue.use初始化插件:  

```
// 插件
const plugin = {
  install(){
	document.write('我是install内的代码')
  }
}
// 初始化插件
Vue.use(plugin); // 页面显示"我是install内的代码"
```
* Vue的插件是一个对象, 就像Element.  
* 插件对象必须有install字段.  
* install字段是一个函数.  
* 初始化插件对象需要通过Vue.use().  
**注意** `Vue.use()调用必须在new Vue之前.`  
<br>&nbsp;

**Vue.use()、Vue.prototype.$xx的区别**  
一个是封装vue插件写法，一个直接挂载拿来即用  
作为插件的install方法里面其实是封装了若干对象的
```
Vue.prototype.$loading = Loading.service;
Vue.prototype.$msgbox = MessageBox;
Vue.prototype.$alert = MessageBox.alert;
···
```
Vue.prototype.$xx  
将$xxx通过定义在Vue.prototype上 实现成为 函数原型上的属性/方法, 在函数实例化后, 可以在任意实例上读取  

axios 不是给Vue定制的方法，里面没有install方法，所以挂载的时候这样
```
const vp = Vue.prototype;
// axios.defaults.withCredentials = true
vp.$http = axios;
```

[参考链接1](https://juejin.im/post/5d15be07e51d4556be5b3a9c)  
[参考链接2](https://www.jb51.net/article/168434.htm)





<br>&nbsp; <br>
## 基本优化
[参考文章](https://juejin.im/post/5f0f1a045188252e415f642c)

### v-show、v-if
* v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
* v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
* 相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
* 一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

**性能**  
如果要频繁切换某节点时，使用v-show（无论true或者false初始都会进行渲染，此后通过css来控制显示隐藏，因此切换开销比较小，初始开销较大），  
如果不需要频繁切换某节点时，使用v-if（因为懒加载，初始为false时，不会渲染，但是因为它是通过添加和删除dom元素来控制显示和隐藏的，因此`初始渲染开销较小`，切换开销比较大）



<br>&nbsp; <br>
### v-for和v-if不要一起使用
vFor 的优先级其实是比 vIF 高的，所以当两个指令出现来一个DOM中，那么 vFor 渲染的当前列表，每一次都需要进行一次 vIf 的判断。而相应的列表也会重新变化，这个看起来是非常不合理的。因此当你需要进行同步指令的时候。尽量使用计算属性，先将 vIf 不需要的值先过滤掉。他看起像是下面这样的。
```
// 计算属性
computed: {
  filterList: function () {
  return this.showData.filter(function (data) {
    return data.isShow
  })
}
```  
```
// DOM  
<ul>
  <li v-for="item in filterList" :key="item.id">
  {{ item.name }}
  </li>
</ul>
```


<br>&nbsp; <br>
### v-for key避免使用index作为标识
其实大家都知道 vFor 是不推荐使用 index 下标来作为 key 的值，这是一个非常好理解的知识点，可以从图中看到，当index作为标识的时候，插入一条数据的时候，列表中它后面的key都发生了变化，那么当前的 vFor 都会对key变化的 Element 重新渲染，但是其实它们除了插入的 Element 数据都没有发生改变，这就导致了没有必要的开销。所以，尽量不要用index作为标识，而去采用数据中的唯一值，如 `id` 等字段。


<br>&nbsp; <br>
### 释放组件资源
什么是资源? 每创建出一个事物都需要消耗资源，资源不是凭空产生的，是分配出来的。所以说，当组件销毁后，尽量把我们开辟出来的资源块给销毁掉，比如 setInterval , addEventListener等，如果你不去手动给释放掉，那么它们依旧会占用一部分资源。这就导致了没有必要的资源浪费。多来几次后，可以想象下资源占用率肯定是上升的。





















<br>&nbsp; <br>
## vue父子通信

### tabbar案例

底部导航组件封装包括 `<Tabbar>` (父)、 `<Item>` (子)

**需求**：  
点击导航组件里代表每个模块的按钮使上方展示不同模块的内容  
**步骤**：  

1. 在App.vue里注册 Tabbar组件和各个页面组件, 

``` 
<script>
import Tabbar from './components/comm/tabbar/Tabbar'
import Home from './views/Home'
import News from './views/News'
import Myself from './views/Myself'
export default {
  components:{
    Tabbar,
    Home,
    News,
    Myself
  }
}
</script>
```

2. 新建Tabbar.vue和Item.vue  

Tabbar.vue  
Tabbar里注册Item组件，通过 `:属性名` 向Item组件传递需要的数据（item的下标文案txt、激活图标、未激活图标）  

``` 
<template>
	<div class="tabberWarp" >
		<div class="warp">
			<Item :txt='item.txt' :page='item.page' @change='getVal' v-for='item in tabbarDes' :sel='selected' :key="item.index">
				<img :src="item.normalImg" slot='normalImg'>
				<img :src="item.activeImg" slot='activeImg'>
			</Item>
		</div>
	</div>
</template>
<script type="text/javascript">
	import Item from './Item'
	export default{
		components:{
			Item
		},
		data:function(){
			return{
				selected:'',
				tabbarDes:[
					{
						txt:'主页',
						page:'home',
						normalImg:require('../../../assets/images/home.png'),
					    activeImg:require('../../../assets/images/home_act.png')
                    },
                    {
						txt:'新闻列表',
						page:'news',
						normalImg:require('../../../assets/images/news.png'),
					    activeImg:require('../../../assets/images/news_act.png')
                    },		
                    {
						txt:'个人中心',
						page:'myself',
						normalImg:require('../../../assets/images/my.png'),
					    activeImg:require('../../../assets/images/my_act.png')
					}
				]
			}
		},
		methods:{
			getVal:function(res){
				this.selected = res;
			}
		},
		mounted(){
			this.getVal('home')
		}
	}
</script>
```

Item.vue  
Item通过 `props:{}` 属性里来声明一个自己的属性，用来从父组件取下相应属性的值

``` 
<template>
	<div class="itemWarp" @click='changePage'>
		<span v-show='!bol'>
			<slot name='normalImg'></slot>
		</span>
		<span v-show='bol'>
			<slot name='activeImg'></slot>
		</span>
		<span v-text='txt' :class="{act:bol}"></span>
	</div>
</template>

<script type="text/javascript">
	export default{
		name: 'Item',
		props:{
			txt:{
				type:String
			},
			page:{
				type:String
			},
			sel:{
				type:String
			}
		},
		computed:{
			bol: function(){
				if(this.sel == this.page){
					return true;
				}
				return false;
			}
		},
		methods:{
			changePage:function(){
				//点击跳转对应的页面
				this.$router.push('/'+this.page);
				this.$emit('change',this.page)
			}
		}
	}
</script
```

3. 通过点击Item来跳转路由，还有需要通过父组件Tabbar去改变其他兄弟Item的样式变化  

子组件通过点击事件 `@click='changePage'` 里的

``` 
this.$emit('change',this.page)
```

告诉Tabbar去处理定义好的 `getVal()` ，  
改变了selected（所有子组件Item都拥有，且值相同）, 
Tabbar再通过 `props:{}` 改变selected, 
引起Item的bol属性的变化来控制样式  
(这里可能你认为可以在Item里控制自己样式的变化就行，但这样却不能使兄弟组件的激活样式发生改变，所以要通过父子通信)

``` 
getVal:function(res){
    this.selected = res;
}
```

[demo gitHub](#)  

<br>&nbsp; </br>

### 总结

`父子组件之间的通信就是 props down, events up，  
父组件通过 属性props向下传递数据给子组件，  
子组件通过 事件events 给父组件发送消息。`  
比如，子组件需要某个数据，就在内部定义一个prop属性，然后父组件就像给html元素指定特性值一样，把自己的data属性传递给子组件的这个属性。  
而当子组件内部发生了什么事情的时候，就通过自定义事件来把这个事情涉及到的数据暴露出来，供父组件处理。  

<br>&nbsp; </br>

## axios

<br>&nbsp; </br>

## rem

在public文件夹的index.html页面模板里加入

``` 
<script>
  (function (l, e) { function g() { var a = d.getBoundingClientRect().width; e || (e = 540); a > e && (a = e); h.innerHTML = "html{font-size:" + 100 * a / l + "px;}" } var a = document, k = window, d = a.documentElement, b, h = document.createElement("style"), f; if (b = a.querySelector('meta[name="viewport"]')) b.setAttribute("content", "width=device-width,initial-scale=1,maximum-scale=1.0,user-scalable=no,viewport-fit=cover"); else if (b = a.createElement("meta"), b.setAttribute("name", "viewport"), b.setAttribute("content", "width=device-width,initial-scale=1,maximum-scale=1.0,user-scalable=no,viewport-fit=cover"), d.firstElementChild) d.firstElementChild.appendChild(b); else { var c = a.createElement("div"); c.appendChild(b); a.write(c.innerHTML); c = null } g(); d.firstElementChild ? d.firstElementChild.appendChild(h) : (c = a.createElement("div"), c.appendChild(h), a.write(c.innerHTML), c = null); k.addEventListener("resize", function () { clearTimeout(f); f = setTimeout(g, 300) }, !1); k.addEventListener("pageshow", function (a) { a.persisted && (clearTimeout(f), f = setTimeout(g, 300)) }, !1); "complete" === a.readyState ? a.body.style.fontSize = "16px" : a.addEventListener("DOMContentLoaded", function (b) { a.body.style.fontSize = "16px" }, !1) })(750, 750);
</script>
```


