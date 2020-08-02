---
title: "JS深入系列"
date: 2020-06-02T22:58:09+08:00
draft: false
categories: ["js"]
tags: ["js原理层"]
---

## type 和 instanceof
typeof `基本类型`和`引用类型`
  
| 类型 | 值 |
| :----| :----|
| Undefined	|"undefined" |
| Null	     |   "object" (历史遗留问题)|
| Boolean	  |      "boolean"|
| Number	   |     "number"|
| String	    |    "string"|
| Object Array   | "object" |


instanceof `运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。`

使用两者结合封装函数来判断类型
```
function getDataType(obj) {
    if(obj === null){
        return "null";
    }else if(typeof obj === "object"){
        if(obj instanceof Array){
            return "array";
        }else{
            return "object";
        }
    }else{
        return typeof obj;
    }
}
```
<br>&nbsp;</br>







## 原型
在典型的oop（面向对象编程）的语言中，如java,都存在类的概念，类就是对象的模板，对象就是类的实例。  
但在js中不存在类的概念，js不是基于类，而是通过`构造函数`(constructor)和`原型链`（prototypechains）实现的。  
但在ES6中引入了类（class）这个概念，作为对象的模板，新的class写法知识让原型对象的写法更加清晰。<br/>

### 构造函数的特点
* 构造函数的首字母必须大写，用来区分于普通函数
* 内部使用的this对象，来指向即将要生成的实例对象
* 使用New来生成实例对象
```
function Person(name,age){
       this.name = name;    
       this.age = age;   
       this.sayHello = function(){   
           console.log(this.name +"say hello");
      }
  }
 var boy = new Person("bella",23);    
 boy.sayHello(); // bella say hello
```

所有的实例对象都可以继承构造器函数中的属性和方法。但是，同一个构造函数构造出来的对象实例之间，无法共享属性

**解决思路：**
* 所有实例都会通过原型链引用到prototype
* prototype相当于特定类型所有实例都可以访问到的一个公共容器
* 那么我们就将重复的东西放到公共容易就好了

**解析**
* 构造函数的prototype属性指向实例原型，实例原型的constructor属性指向回构造函数
* 对象实例的__proto__属性指向实例对象
```
function Person(name,age){
      this.name = name;
      this.age = age;
  }
  Person.propotype.sayHello = function(){
      console.log(this.name + "say hello");
  }
  var girl = new Person("bella",23);
  var boy = new Person("alex",23);
 console.log(girl.name);  //bella
 console.log(boy.name);   //alex
 console.log(girl.sayHello === boy.sayHello);  //true
```

### prototype
js中每个数据类型都是对象，除了null 和 undefined,而每个对象都是继承自一个原型对象，只有null除外，它没有自己的原型对象，最终的Object的原型为null
### 对象的分类
JS中万物都是对象，但是对象也分为：普通对象和函数对象，也就是`Object` 和 `Function`  
那么怎么区分普通对象和函数对象呢？  
凡是通过New Function()创建的对象都是函数对象，其他的都是普通对象.需要注意的是：普通对象没有propotype（prototype即是属性也是对象），但是有__proto__属性。<br>

![示意图](https://user-gold-cdn.xitu.io/2020/5/24/17244f88fbc4699c?w=581&h=602&f=png&s=64089)


<br>&nbsp;</br>
## 作用域
js的作用域是静态作用域（词法作用域）--`从在哪里定义开始`  
f1在定义的时候， js解析器会给f1定义一个内部的属性叫scope, 按照f1定义时的词法环境，scope是指向window的，所以当f2调用f1的时候，程序首先会在f1的函数体内寻找变量v， 没找到， 就去window中去寻找v，也没有变量v，所以程序就报错了。  
也就是说，f1的作用域是在定义的时候就已经决定了，而不是在调用时决定的。这也就是所谓js的静态作用域
```
function g(){
    var v=100
    function f(){
        console.log(v)
    }
    f()
}
g() // 100
```


<br>&nbsp;</br>
## 执行上下文栈 
JavaScript 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。



<br>&nbsp;</br>
## 变量声明
* 函数声明  
由名称和对应值（函数对象(function-object)）组成一个变量对象的属性被创建  
如果变量对象已经存在相同名称的属性，则完全替换这个属性  
* 变量声明  
由名称和对应值（undefined）组成一个变量对象的属性被创建；  
如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性

案例一:函数中只有var 才会变量声明提前
```
function f(){
    console.log(a) // Uncaught ReferenceError: a is not defined,报错下面不会执行
    a=1 
}
f()
console.log(a)
```
```
function f(){
 console.log(a) //undefined
 var a=1 // 变量声明提升,声明为函数f的局部变量
}
f()
console.log(a) // Uncaught ReferenceError: a is not defined,函数外部访问不到a
```
案例二:函数内变量没有，能执行到变量赋值处，该变量就成为全局变量
```
function f(){
 g=1
}
f()
console.log(g)
```



<br>&nbsp;</br>
## 作用域链
当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做`作用域链`。



<br>&nbsp;</br>
## 闭包
闭包是指那些能够访问自由变量（函数参数也不是函数的局部变量的变量）的函数。<br>  组成：`闭包 = 函数 + 函数能够访问的自由变量`  
ECMAScript中，闭包指的是：  
* 从理论角度：所有的函数。因为它们都在创建的时候就将上层上下文的数据保存起来了。哪怕是简单的全局变量也是如此，因为函数中访问全局变量就相当于是在访问自由变量，这个时候使用最外层的作用域。
* 从实践角度：以下函数才算是闭包：  
1.`即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）`  
2.`在代码中引用了自由变量`  

```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }   
    return f;
}

var foo = checkscope();
foo();
```
当我们了解了具体的执行过程后，我们知道 f 执行上下文维护了一个作用域链：

```
fContext = {
    Scope: [AO, checkscopeContext.AO, globalContext.VO],
}
```
对的，就是因为这个作用域链，f 函数依然可以读取到 checkscopeContext.AO 的值，说明`当 f 函数引用了 checkscopeContext.AO 中的值`的时候，即使 checkscopeContext 被销毁了，但是JavaScript 依然会让 checkscopeContext.AO 活在内存中，f 函数依然可以通过 f 函数的作用域链找到它，正是因为 JavaScript 做到了这一点，从而实现了闭包这个概念。



<br>&nbsp;</br>
## call，apply，bind






<br>&nbsp;</br>
## new
参考链接：  
[能否模拟实现JS的new操作符](https://zhuanlan.zhihu.com/p/50719681)






<br>&nbsp;</br>
## 继承