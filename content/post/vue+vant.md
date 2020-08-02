---
title: "Vue+vant"
date: 2020-06-30T13:59:25+08:00
draft: false
categories: ["学习笔记"]
tags: ["vue"]
---

vant是基于vue开发的移动端 Vue 组件库, 方便我们在开发项目时少些代码，加快生产效率

## vant-tabbar

在vue项目里tabbar通常结合着vue-router来使用， `路由模式` 达到单页面切换模块的效果  
**步骤**： 

* 前提要先配置好路由

``` 
Vue.use(VueRouter)
const routes = [
    {
        path: '/home',
        name: 'home',
        component: home,
    },
    {
        path: '/activity',
        name: 'activity',
        component: activity,
    },
    {
        path: '/my',
        name: 'my',
        component: my,
    },
    {
        path: '/',
        redirect: '/home'
    },
]
const router = new VueRouter({
    routes
})
export default route
```

* 在main文件全局引入vant-tabbar组件

``` 
import 'vant/lib/index.css';
import {
  Tabbar, TabbarItem
} from 'vant';
Vue.use(Tabbar).use(TabbarItem)
```

* App.vue

``` 
<van-tabbar class="g-nav" route="true">
	<van-tabbar-item replace to="/home" icon="home-o">首页</van-tabbar-item>
	<van-tabbar-item replace to="/activity" icon="newspaper-o">文章</van-tabbar-item>
	<van-tabbar-item replace to="/my" icon="setting-o">我的</van-tabbar-item>
</van-tabbar>
```

关键属性：  
van-tabbar  
`:route="true"` 是否开启路由模式  
van-tabbar-item  
`replace` 是否在跳转时替换当前页面历史，不写上默认为false，加上时即在切换tabbar时进退键不可用    
`to` 点击后跳转的目标路由对象，同 vue-router 的 to 属性  
`icon` 图标名称或图片链接(可在vant Icon组件里引用一些定制好的图片)

<br>&nbsp; </br>

## vant-swipe

* 在main文件全局引入vant-tabbar组件

``` 
import {
  Swipe,
  SwipeItem,
  Lazyload 
} from 'vant';
Vue.use(Swipe).use(SwipeItem)
.use(Lazyload)
```  

* 在需要swipe组件的父组件里引用 `<banner>` ,同时模拟下接口返回数据

```

<banner :banners="banners"></banner>

``` 
```
data() {
    return {
      banners:[],
      banners2: [
        {
          image: "https://dummyimage.com/750x400?text=banner1",
          url: "https://www.baidu.com/",
          name:'图1'
        },
        {
          image: "https://dummyimage.com/750x400?text=banner2",
          url: "",
          name:'图2'
        },
        {
          image: "https://dummyimage.com/750x400?text=banner3",
          url: "https://www.taobao.com/",
          name:'图3'
        }
      ],
      
    };
  },
  mounted(){
    let that = this
    // 模拟接口返回数据
    setTimeout(function(){
      that.banners=that.banners2
    },3000)
```

* 将项目中的轮播图提取作为一个公共组件banner.vue  

每个swipe-item有对应的链接，标题，图片, 图片配合 `Lazyload` 懒加载使用v-lazy="img"

``` 
<template>
  <div class="compo-banner">
    <van-swipe :autoplay="3000" class="banner-list" @change="onChange">
      <van-swipe-item class="banner-item" :key="index" v-for="(item, index) of cBanners">
        
        <a :href="item.url" v-if="item.url">
          <img v-lazy="urlPrefix?item.image :item.img" :alt="item.name" class="banner-img">
        </a>

        <img v-lazy="urlPrefix?item.image :item.img" :alt="item.name" class="banner-img" v-else>

        <p class="banner-tit">{{item.name}}</p>
      </van-swipe-item>

      <!-- 通过 indicator 插槽可以自定义指示器的样式。 -->
      <div class="banner-indicator" v-if="cBanners.length" slot="indicator">
        {{ curIndex + 1 }}/{{cBanners.length}}
      </div>

    </van-swipe>
  </div>
</template>

<script>
  export default {
    name:'banner',
    props: ['banners'],
    data(){
      return {
        urlPrefix:false,
        curIndex: 0,
        cBanners:[{
          img:'https://dummyimage.com/750x400',
          url:'',
          name:'缺省标题'
        }]
      }
    },
    created(){
      console.log(this.cBanners)
    },
    methods:{
      onChange(index){
        this.curIndex = index
      }
    },
    
    watch:{
      banners(newValue){
        this.urlPrefix = true
        this.cBanners = newValue
      }
    },
    mounted(){
      console.log(this.urlPrefix)
    }
  }
</script>
```

关键属性：  
van-swipe  
`@change="onChange"` 每一页轮播结束后触发, index: 当前页的索引

<br>&nbsp; </br>

## van-icon

基于字体的图标集, 可以选择多种图标使用  
https://youzan.github.io/vant/#/zh-CN/icon













<br>&nbsp; </br>
## van-skeleton

Skeleton 骨架屏是多数h5项目中会用到的内容占位，减少因为内容加载时间过长让人感觉页面加载慢  

``` 
<van-skeleton :loading="outerLoading" :row="5">

  <rankList :rankList="rankList" class="compo-wrap"></rankList>

  <router-link to="prankList">
    <div class="see-more">查看完整排行</div>
  </router-link>

</van-skeleton>
```
关键属性：  
`:loading=""/loading`  将loading属性设置成false表示内容加载完成，此时会隐藏占位图，并显示Skeleton的子组件  
`row`	段落占位图行数  
`animate` 是否开启动画  









<br>&nbsp; </br>
## van-list
瀑布流滚动加载，用于展示长列表，当列表即将滚动到底部时，会触发事件并加载更多列表项  
通常放在`<van-skeleton>`里配合使用  
```
<van-skeleton :row="15" :loading="outerLoading">

    <van-list v-model="loading" :offset="30" :finished="finished" finished-text="没有更多了" @load="loadMore">
      <compoRankList :rankList="rankList" class="compo-wrap sec-base"></compoRankList>
    </van-list>
    
</van-skeleton>
```
通过`loading`和`finished`两个变量控制加载状态，当组件滚动到底部时，会触发load事件并将loading设置成true。  
此时可以发起异步操作并更新数据，数据更新完毕后，将loading设置成false即可。  
若数据已全部加载完毕，则直接将finished设置成true即可。  
&nbsp;   
关键属性：  
 `v-model="loading"`是否处于加载状态，加载过程中不触发load事件，默认false  
 `:offset="30"`滚动条与底部距离小于 offset 时触发load事件  
 `:finished`是否`全部`已加载完成，加载完成后不再触发load事件  
 `finished-text="没有更多了" `  
 `@load="loadMore"` （方法）滚动条与底部距离小于 offset 时触发  



















