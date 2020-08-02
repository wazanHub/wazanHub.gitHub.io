---
title: "一个简单的仿群聊h5交互小页面"
date: 2020-06-08T11:49:28+08:00
draft: false
categories: ["vue"]
tags: ["项目总结"]
---


## 介绍
&emsp;&emsp;<font color="#888">之前某祺汽车客户搞系列线上留资活动，其中有个类似机器人的仿微信群聊h5需求,提前用jq写出来了，可以说完成了基本的功能(也没什么功能)。后来听说需求腰斩没上线，闲来有空用vue重构下(其实这么简单的h5似乎没必要用vue来做，这里是自己因为对vue的不熟悉，以此来练习并加对知识点的总结)</font><br>

[线上地址](https://www1.pcauto.com.cn/test/wtest/v-chat/index.html)  
或扫码体验  
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b3a74be77774cea84e1b03412af23ac~tplv-k3u1fbpfcp-zoom-1.image)




## <font face="DFKai-SB">结果都一样,最重要的是过程</font>  
参考案例是大概是小米做的一个互动h5页面。在下

![](https://user-gold-cdn.xitu.io/2020/6/27/172f6021a3e8ced1?w=135&h=307&f=png&s=8533)
![](https://user-gold-cdn.xitu.io/2020/5/14/1721395b24f9d292?w=267&h=131&f=png&s=12946)

## 原需求分析
* <font color="#888">入口添加用户名或者先微信授权进入，获取微信头像，名称等信息</font>
* <font color="#888">进入活动页面，显示日常微信进群的样式，自动进行开场对白聊天</font>
* <font color="#888">首段聊天对白结束后，底下输入功能区变为若干提问的问题点，手动点击又会出现自动的对白聊天</font>

<center class="half">
    <img src="https://user-gold-cdn.xitu.io/2020/5/14/17213af392e6e9ab?w=200&h=120&f=png&s=23919" width="200"/>
    <img src="https://user-gold-cdn.xitu.io/2020/5/14/17213a623e28a5e1?w=200&h=136&f=png&s=20968" width="200"/>
    <img src="https://user-gold-cdn.xitu.io/2020/5/14/17213b0a62cd7157?w=310&h=130&f=png&s=9532" width="200"/>
</center>


## 开干思路
##### &emsp;&emsp;主要开发点：

* <font color="#888">切换计算渲染相应内容的容器组件尺寸</font>
* <font color="#888">每出一条最新对话记录使聊天页面滑到最底处</font>
* <font color="#888">定时加载输出既定对白记录</font>
*  注意点：每段对白结束前不可点击下面的提问按钮
[vue元素获取](#) [容器各种高度](#)

## 代码回顾
这里大概说下过程中的思路和遇到的问题点，不对着逻辑代码做一段一段review，感觉语焉不祥，所以都写在代码的注释里
### 1.vue-cli搭建项目

### 2.确定组件结构
一级：App  
二级：Main(聊天记录展示区)、Bottom(底部控制输出区)  
三级：Record(每段对白群)

### 3.页面容器尺寸分配渲染
<img src="https://user-gold-cdn.xitu.io/2020/5/14/17213e114239d7ce?w=494&h=548&f=png&s=55387" width="300"/><br>
<font color="#999">
明显Bottom的高度无论横屏竖屏都应该是固定的，变化的是Main的高度。<br>
这里写个方法在初始化或者监听窗口发生变化时计算并渲染这两块容器的高度<br>
</font>
<b>做法</b>：<br>
在App钩子函数mounted里使用这个方法[钩子用法](#)<br>
<font color="#999">
在父组件App渲染完成即App mounted后(子组件已在此之前mounted完)，开始调用frameChange()里的frameInit(),得到<br>组件Main的高度=窗口的高度-确定的底部Bottom组件的高度
</font>
```
mounted(){
    this.frameChange()
  } 
```
```
frameChange() // 尺寸变化所做操作
frameInit() //计算并渲染布局Main尺寸
```
### 对白数据从哪里来
Mockjs模拟接口数据[Mock数据用法](#)<br>
在子组件Record的mouted里调用createData获取数据<br>
[钩子用法](#)&emsp;[vuex的应用](#)
```
<!--Record-->
mounted(){
        this.createData()
    }
```

### 使用vuex创建一个公共状态
写一个Promise执行从获取到渲染数据的过程<br>


### Main的子组件Record,即每条记录的展示
<font color="#888">1.进来自动加载一段对白<br>
2.实现定时加载这段对白里的每条内容<br>
3.在使用定时器加载的时间里控制底下Bottom组件不能被点击<br>
</font>
(在这里就不review这部分的代码了,在源码里作了详细注释,感觉结合代码更利于理解)



## 项目总结
通过这个小练习对vue的一些api在项目的使用有了个认识  
`1.对于组件及其组件里的节点的获取`  
`2.对组件的钩子函数的理解与应用`  
`3.对于mockjs的使用`  
`4.对于promise的使用`  
`5.对于vuex的使用`


## 源码地址
[v-chat仓库地址](https://github.com/wazanHub/v-chat)  



![](https://user-gold-cdn.xitu.io/2020/6/27/172f60269691336e?w=135&h=307&f=png&s=8533)




