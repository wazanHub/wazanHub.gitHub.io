---
title: "npm相关"
date: 2020-06-27T12:09:54+08:00
draft: false
categories: ["工具应用"]
tags: ["npm"]
---
  
node.js下的包管理工具

## 镜像安装
国内网络环境下载包网速慢的处理
* 使用淘宝镜像

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
npm指向淘宝镜像
```
npm config set registry https://registry.npm.taobao.org/  //淘宝源

npm config set registry https://registry.npmjs.org/ //改回原始源 

npm config get registry //配置后可通过下面方式验证是否成功 
```

## 包安装
### 本地安装与全局安装
`全局安装`一般用于框架工具类的安装，如vue、 webpack等  
1. 将安装包放在 /usr/local 下  
2. 可以直接在命令行里使用  
```
npm i webpack -g    
```

`本地安装`一般用于项目依赖的安装，分为安装到开发环境、生产环境
1. 将安装包放在 ./node_modules 下（运行npm时所在的目录）
2. 可以通过 require() 来引入本地安装的包
```
// 安装到开发环境,写入到 devDependencies 对象

npm i jquery --save-dev 

npm i jquery -D


//安装到生产环境,写入到 dependencies 对象

npm i jquery

npm i jquery --save

npm i jquery -S
```

