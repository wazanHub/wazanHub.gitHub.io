---
title: "webpack简单入门"
date: 2020-06-27T11:43:48+08:00
draft: false
categories: ["工具应用"]
tags: ["webpack"]
---

## 使用webpack的好处

<br>&nbsp;</br>
## 目录构建

``` 
webpack init -y // 初始化

npm i webpack -D // 开发环境安装webpack

mkdir src //创建相关开发目录及入口文件

.>webpack.config.js // 配置文件
```

![](https://user-gold-cdn.xitu.io/2020/6/27/172f60269691336e?w=135&h=307&f=png&s=8533)

<br>&nbsp;</br>
## webpack基础配置

### 配置入口

webpack.config.js里配置文件

``` 
entry: {
    
    app1:path.join(__dirname, 'src/js/app1.js'),

    app2:path.join(__dirname, 'src/js/app2.js')

},

output: {
    
    path: path.join(__dirname, 'dist'),
    
    filename: '[name].bundle.js?[hash:5]'

},

module:{},

plugins:[]
```

<br>&nbsp;</br>
### 开发环境部署服务

安装webpack-dev-server

``` 
npm i webpack-dev-server -D 
```

配置启动命令

**法一**：package.json文件里配置

``` 
"dev": "webpack-dev-server --open --port 3000 --contentBase src --hot",
```

**法二**：在webpack.config.js里配置

``` 
devServer: {

    contentBase: 'src', // 指定托管的根目录
    
    inline: true, // 自动刷新
    
    hot: true, // 启动热更新
    
    port: 5000, // 设置启动的时候的运行端口
    
    open: true // 自动打开开浏览器

}
```
<br>&nbsp;</br>
### webpack.config.js

`当我们在控制台，直接输入 webpack命令执行的时候，webpack做了以下操作` 

1. 首先webpack发现，我们并没有通过命令的形式，给它指定入口和出口
2. webpack就会去项目的根目录去查找一个叫'webpack.config.js'的配置文件
3. 当发现配置文件后，webpack会去解析执行这个配置文件，当解析执行完成配置文件后，就得到配置文件中导出的对象
4. 当webpack拿到配置对象后，就得到了配置文件中指定的入口和出口，然后进行构建打包

`使用 webpack-dev-server 这个工具，来实现自动打包编译的功能` 
 1. npm i webpack-dev-server -D
 2. webpack这个工具正常运行必须在本地项目中安装webpack
 3. webpack-dev-server 帮我们打包生成的 bundle.js 文件，并没有存放在实际的物理磁盘上，而是
 直接托管到电脑的内存中(快)，所以，我们在项目根目录中，根本找不到这个打包好的bundle.js；
 4. 我们可以认为，webpack-dev-server 把打包好的文件，以一种虚拟的形式，托管到了项目的根目录中，虽然我们看不到它，但是可以认为
 和dist src平级，有一个看不见的文件bundle.js

<br>&nbsp;</br>
### 开发环境下的预览
刚开始npm run dev, 需要在src下新建文件index.html, 同时引入开发环境下打包生成的js  
现在我们期望以已有的index.html作为模板，生成自动引入js的index.html（生成的index.html src不可见,dist下可见）

1. 导入生成的 html 页面的插件

``` 
npm i html-webpack-plugin -D
```

2.webpack.config.js里配置下这个插件

``` 
// 作用：

// 1. 自动在内存中根据指定页面生成一个内存的页面

// 2. 自动把打包好的 bundle.js 追加到页面中去

const webpack = require("webpack");

const HtmlWebpackPlugin = require('html-webpack-plugin');

// 配置插件的节点

plugins: [ //是个数组

    // 创建一个在内存(物理磁盘)中生成的html页面的插件

    new HtmlWebpackPlugin({

        title: 'index',

        template: path.join(__dirname,'./src/index.html'),// 指定模板页面，将来会根据指定的页面路径，去生成内存（物理）中的页面
        
        hash: false,//hash选项的作用是 给生成的 js 文件一个独特的 hash 值,也可以设置为false，在上面的output的js文件尾加?[hash:8]
        
        filename: 'index.html',//开发环境（内存中）和生产环境的index.html都被替换
        
        // favicon: 'path/to/yourfile.ico',//网页图标
    
    })

]
```







<br>&nbsp;</br>
## 处理资源文件  
加载非js的文件
webpack都是打包成js文件的，里面包含css，image等，此时需要安装配置相应的加载器

### css文件

js引入css文件

``` 
import '../css/style.css'
```

安装css-loader style-loader

``` 
npm i style-loader css-loader -D
```

webpack.config.js里配置

``` 
module: {
    rules: [
        {
            test: /\.css$/,
            use: [
                'style-loader',// 可以把css放在页面上
                'css-loader'// 放在后面的先被解析
            ]
        }
    ]
},
```


css间的相互引用
``` css
@import "globals";
```

 
### 加载图片  
引入file-loader

### 加载sass文件
```
npm i sass-loader -D
```

```
{
    test: /\.scss$/,
    use: [
        'style-loader',
        'css-loader',
        'sass-loader'
    ]
}

```
`注意`
直接运行会报错，提示 Cannot find module 'node-sass',  
这里要使用cnpm安装node-sass，不然npm安装一般失败


### 处理ES6语法的文件



### 处理css前缀




<br>&nbsp;</br>
## 生成html页面文件
### webpack-html-plugin插件
可携带参数给生成的html文件
```
new HtmlWebpackPlugin({
    
    title: 'index',
    
    template: path.join(__dirname,'./src/index.html'),// 指定模板页面，将来会根据指定的页面路径，去生成内存（物理）中的页面
    
    hash: false,//hash选项的作用是 给生成的 js 文件一个独特的 hash 值,也可以设置为false，在上面的output的js文件尾加?[hash:8]
    
    filename: 'index.html',//开发环境（内存中）和生产环境的index.html都被替换
    
    // favicon: 'path/to/yourfile.ico',//网页图标
    data:'1232',
    
    date: new Date(),
    
    minify:{
        
        removeComments:true,// 删除注释
        
        collapseWhitespace:true // 删除空格
    }

}),
```

src下的index.html模板
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title> <%= htmlWebpackPlugin.options.title %></title>
</head>
<body>
    <div>
        <%= htmlWebpackPlugin.options.data %>
        <!-- 注释 -->
        <% for(var key in htmlWebpackPlugin.files) {%>
            <%= key %> : <%= JSON.stringify(htmlWebpackPlugin.files[key])%>
        <% } %>    
    </div>
</body>
</html>
```


<br>&nbsp;</br>
### 按照模板生成不同的html  
生成不同的html的同时，给不同的html指定需要加载的js
在本地服务器托管的目录下和最后打包的dist下  生成的html指定需要的js  
webpack.config.js
```
new HtmlWebpackPlugin({
    
    template: path.join(__dirname,'./src/index.html'),// 指定模板页面，将来会根据指定的页面路径，去生成内存（物理）中的页面
    
    filename: 'index.html',//开发环境（内存中）和生产环境的index.html都被替换
    
    chunks:['app1'] //为index.html指定加载app1.bundle.js,    这里是个数组，可以是多个js

}),
new HtmlWebpackPlugin({
    
    template: path.join(__dirname,'./src/index.html'),// 指定模板页面，将来会根据指定的页面路径，去生成内存（物理）中的页面
    
    filename: 'index2.html',//开发环境（内存中）和生产环境的index.html都被替换
    
    chunks:['app2'] //为index2.html指定加载app1.bundle.js

})
```




<br>&nbsp;</br>
### 给生成的不同的html指定不同的css文件
































<br>&nbsp;</br>
### 其他插件
* clean-webpack-plugin  
执行npm run dev时会清空dist
``` 
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

plugins: [ //是个数组
    
    new CleanWebpackPlugin(),//新的文档用法,  当npm run dev时会清空build

]
```




<br>&nbsp;</br>
### publicPath  
如果在output里配置publicPath(用于生成js上线的地址)
```
    output: {

        path: path.join(__dirname, 'dist'),
        
        filename: '[name].bundle.js?[hash:10]',
        
        publicPath: 'http://cdn.com/' //设置生成的js文件的地址目录
    
    },
```
生成的html文件里会自动替换
```html
    <script src="http://cdn.com/app1.bundle.js?157377155c"></script>
    <script src="http://cdn.com/app2.bundle.js?157377155c"></script>

```













