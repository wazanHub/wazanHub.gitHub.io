---
title: "转盘抽奖"
date: 2020-06-29T14:28:13+08:00
draft: false
categories: ["项目"]
tags: ["项目总结"]
---
**`好的项目逻辑开发要记下备用`**   

<br>&nbsp;</br>
疫情期间，销售拉单“无底线”，产品策划很“无限”，各种活动满足米饭班主。  
这是做过的在线最久的活动，最初开始做是4月完成的一版，现在也没下线。

项目由我们这边开发完成，然后提供html（外链了上传到我们服务器的各种资源）给某祺汽车的官网老师上传到他们服务器生成他们域名的链接。 

## 活动地址
线上链接：[传祺GS4 3999出行补贴天天抽](https://www.gacmotor.com/activity2020/index.html)  
线下链接：[传祺GS4 3999出行补贴天天抽](https://www.gacmotor.com/activity2020gift/)


<br>&nbsp;</br>
## 总体需求
* 填写信息留资成功即可参加抽奖
* 每个号码有3次抽奖机会(转3次转盘)
* 奖品为不同类型的券的兑换码




<br>&nbsp;</br>
## 开发思路  
### 分为2个页面 ###
* 首页index.html
* 活动页main.html  
其中活动页的业务显然很多，这里采用了分为不同页面层显示隐藏的模式，类似于vue的单页面开发。具体可分为：  
`报名页模块`  
`转盘抽奖页模块`  
`奖品选择领取页模块`  
`已领取兑换码展示页模块`    

将这些模块都写在main.html里再通过显示隐藏来实现，选择这样开发有优缺点   
**优点：**  
1.对每个模块的关联数据更加方便处理，降低开发难度（不然只能通过url去传递关联数据，相比之下vue的路由传参和vuex就能体现场景优势）  
2.而且能减少不同页面跳转带来切换不流畅的不良体验，和减少不同机型在切换时出现的一些兼容问题  
**缺点：**    
1.全部图片样式资源写在main.html里必然造成请求和加载量多。只能通过尽量使用公共背景图片或者制作雪碧图等优化  
2.全部页面逻辑写在一个页面，必然造成不同模块逻辑的相互影响


<br>&nbsp;</br>
## 转盘抽奖页模块
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b4e52ccdeeca4f29ae77d2e3dfbbad57~tplv-k3u1fbpfcp-zoom-1.image)
### html、js分离整理 ###
别看只有3次抽奖，但注意业务逻辑还真不少，比如：   
* 没抽奖前该显示什么，  
* 每抽完一次要显示的结果是什么，文案要合理  
* 没用完3次机会前退出或者刷新页面需要带来的变化  
* 机会用完时该怎样处理  
* ...  
当时想到了分离html、js的做法，    
1.先把html部分不同情况的抽奖结果模块化写出来，   
```html
    <!-- 还没开始抽 -->
    <div class="result start">
      <p class="txt3"><span class="licon"></span>您有<b class="remainTimes">0</b>次抽奖机会<span class="ricon"></span></p>
    </div>

    <!-- 中奖还有机会，继续抽 -->
    <div class="result prize-again">
      <p class="txt1">恭喜您获得：</p>
      <p class="txt2"></p>
      <p class="txt3"><span class="licon"></span>您还有<b class="remainTimes">0</b>次抽奖机会<span class="ricon"></span></p>
      <div class="btn_again play"></div>
    </div>

    <!-- 不中奖还有机会，继续抽-->
    <div class="result noprize-again">
      <p class="txt2">很遗憾，你未中奖</p>
      <p class="txt3"><span class="licon"></span>您还有<b class="remainTimes">0</b>次抽奖机会<span class="ricon"></span></p>
      <div class="btn_again play"></div>
    </div>



    <!-- 中奖没有机会了-->
    <div class="result prize-end">
      <p class="txt1">恭喜您获得：</p>
      <p class="txt2"></p>
      <p class="txt3"><span class="licon"></span>机会已用完，请点击上方"我的奖品"领取奖励<span class="ricon"></span></p>
    </div>

    <!-- 没中奖没有机会了-->
    <div class="result noprize-end">
      <p class="txt2">很遗憾，你未中奖</p>
      <p class="txt3"><span class="licon"></span>机会已用完，请点击上方"我的奖品"领取奖励<span class="ricon"></span></p>
    </div>

    <!-- 再次进来没有机会了-->
    <div class="result end">
      <p class="txt3"><span class="licon"></span>机会已用完<span class="ricon"></span></p>
    </div>

    <!-- 再次进来还有机会了-->
    <div class="result no-end">
      <p class="txt3"><span class="licon"></span>您还有<b class="remainTimes">0</b>次抽奖机会<span class="ricon"></span></p>
      <div class="btn_again play"></div>
    </div>
```

2.然后再在js里对不同的情况做下判断处理每次将这些模块化显示影藏  

这种做法明显会让你更快地搞清楚逻辑需求，明确自己的开发步骤，尤其在后面还有其他关联业务减少混乱，避免不同情况缺少这个那个文案，按钮之类








<br>&nbsp;</br>
## 添加验证码
活动上线之后发现有人故意刷数据，决定提高参与门槛  
首先从后端搬来短信验证码，后来发现这样也不行，这个h5活动没有账号密码限制要防刷实在太难，短信验证也可以被恶意输入任意号码去调接口，每条短信都是需要money的，  
`图片证码+短信验证码`  
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/91e01265d4ad4c009e1e8fc21f44493d~tplv-k3u1fbpfcp-zoom-1.image)
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d0c4cf85b58646cfa4ecc2dfdfd81a48~tplv-k3u1fbpfcp-zoom-1.image)


最后商量决定先加图形验证码，图形验证成功再允许调取短信接口
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a733defe63c4452e9e0f27827fdf748f~tplv-k3u1fbpfcp-zoom-1.image)

**注意点：**
  这里是直接src去请求图片验证码，如果是看不清等情况的验证码还要点击能获取新的，这里给src图片请求后面加上个时间戳，这样才会再次去请求返回验证码
  ```
      getPic: function(fn) {
        $('.ycode').val('');
        var time=new Date().getTime();
        var purl="//play9.pcauto.com.cn/auto200165/api/auth/validateCode.do?phone="+$('.i2').val()+'&time='+time;
        $('.ypic').html('<img src='+purl+'/>');
    },
  ```

  这里还给获取图形验证码的按钮加个1min的延时，如果请求获取短信验证码的1min里不能请求

  ![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3850e9747dab45aa8ddde40c7fb28f43~tplv-k3u1fbpfcp-zoom-1.image)
  ```
  getMessageCaptcha: function() {
        $.ajax({
            url: comm.action + 'getMessageCaptcha.jsp',
            type: 'POST',
            data: {
                ischeck: false,
                phone: $('.i2').val(),
                keyCode:$('.ycode').val()
            },
            xhrFields: {
                withCredentials: true
            },
            success: function(data) {
                if (data.code == 1) {
                    $('.ybox').hide();
                    $('.fixed1').hide();

                    $('.ybtn').hide();
                    $('.un-ybtn').show();
                    var i = 60;
                    comm.flag1=false
                    var inter = setInterval(function() {
                        i--;
                        $('.un-ybtn').html('重新发送(' + i + ')');
                    },
                    1000);
                    setTimeout(function() {
                        clearInterval(inter);
                        $('.ybtn').show();
                        $('.un-ybtn').hide();
                        comm.flag1=true;
                    },
                    60000);
                } else if(data.code=-4){
                    comm.tips(data.msg);
                    comm.getPic();
                } else {
                    comm.tips(data.msg);
                }

            },
            error: function() {
                comm.tips('网络不佳，请稍后重试');
            }
        });
    },
  ```






<br>&nbsp;</br>
## 资源
[项目源码]()


