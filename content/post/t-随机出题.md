---
title: "实现随机出题"
date: 2020-06-29T14:28:13+08:00
draft: false
categories: ["项目"]
tags: ["项目总结"]
---
**`好的项目逻辑开发要记下备用`**   

<br>&nbsp;</br>
年初疫情肆虐，在家办公赶一个上汽大众答题抢返现券支付宝活动，最近活动翻新出第二版，需求变得简单了，相比第一版开发精华都不需要，保留了基本的样子。
找出源码review下，感觉有必要做下项目总结


## 总体需求
* 正常套路，线上扫码进入活动页，
* 答题达到合格分数60分可领购车券，
* 未及格通过将活动分享好友获取再次抽奖机会，直到领券成功。  
* 答完全部题提交可查看答案



<br>&nbsp;</br>
## 开发思路  
**这里将活动分为3个页面**
* 首页index.html
* 活动页enter.html
* 分享页share.html  

**主要需求在enter.html**
* `实现22道题中出任意10道题的效果`：通过将n个元素的数组随机排序，再截取前m位来实现达到同样随机性目的
* `分享获取机会`：从 getUserInfo.jsp 获取用户uid，分享的时候利用二维码插件生成带有该uid的二维码的分享图，朋友通过扫该分享图进入活动即算助力前者。
（前端只需生成携带uid的分享图，剩下后端搞定即可）



**以下是进入活动页enter.html**  
先请求后台接口`getUserInfo.jsp`获取用户信息，对用户当前状态进行判断：  
捋下思路做个流程图如下：
![活动](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/06e57dba912747f091b36e23b7419e25~tplv-k3u1fbpfcp-zoom-1.image)
  

<br>&nbsp;</br>
## 实现出题需求
需求方提供22道题目，其中10道汽车类，12道防疫类，要求各取`任意5道题`，组成10道题目，每道题10分，最后成绩60分以上算合格。  
![活动](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c5a0101c1ed4bef9a828c24724a3935~tplv-k3u1fbpfcp-zoom-1.image)

（要是能直接从后端接口获取题目就省事多了，但开发小哥并没有这样做，出题这块只能由我来实现，从难度上讲感觉实现这个前后端是一样的）  

首先这里22道题只出10道，就要符合`排列组合`的顺序的 随机性，那样有多种可能。  

**换个角度思考：**
我们只要满足 `汽车题随机性` 和 `防疫题的随机性` 两个就可以，  
那我们通过打乱题目数组的顺序，然后取前10个元素不就同样满足这种随机性  
做法：  
1.打乱数组顺序的方法
```
randomSort:function(arr){

    // 利用数组排序方法sort（）,
    
    arr.sort(function(a,b){
        
        // Math.random()返回[0,1)的任意一个小数 - 中间数0.5 = 有两种可能 >0 || <0
        
        return Math.random() - 0.5;
    
    });
    
    return arr;
},
```
2.新建一个参考数组oldArr 代表题目数组，用数字大小才能进行sort（）比较，随机排列各个数组元素,获取前5个元素
```
// 10道汽车题中
getNewArr1:function(arr){

    var oldData=arr,newData=[],
    
    oldArr=[0,1,2,3,4,5,6,7,8,9],newArr=[];
    
    //新随机排序数组
    newArr=comm.randomSort(oldArr)
    
    //取前面5道
    for(var i=0;i<5;i++){
        
        newData.push(oldData[newArr[i]])
    
    }
    
    return newData;
},
// 12道防疫题处理做法类似
```
concat()组合获取到的两个新题目数组,一样进行数组排列，得到最终题目数组`finalItems`
```
//随机排列结合的数组元素,获取前10个元素
getItems:function(){

    var arr1=comm.getNewArr1(data1);
    
    var arr2=comm.getNewArr2(data2);

    var items=arr1.concat(arr2);

    var finalItems=[],oldArr=[0,1,2,3,4,5,6,7,8,9],newArr=[];
    
    newArr=comm.randomSort(oldArr)//新随机排序数组
    
    for(var i=0;i<10;i++){
        
        finalItems.push(items[newArr[i]])
    
    }
    return finalItems;
},
```
3.最后根据数组渲染成dom元素




<br>&nbsp;</br>
## 页面展示
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d32ea5637c0a404bb25f43d2b383edce~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ccd8185181647b4b0050b79e7fc0758~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/50bd17964b304d4c92de26c9e60d2efc~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/60587b53101543cfb090ad67e3d83271~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/29c6933966d341a9ae028929ff9407b2~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/640d5904b2fb440c8edc90150fc5d2cf~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0517ff495f184c019a40c2a8611f570c~tplv-k3u1fbpfcp-zoom-1.image)


<br>&nbsp;</br>
## 资源地址
[线上地址](https://www1.pcauto.com.cn/zt/gz20200210/daz/index.html)(活动已下线，后端控制进不了活动页)  
[源码地址](https://github.com/wazanHub/daz-zfb)  




