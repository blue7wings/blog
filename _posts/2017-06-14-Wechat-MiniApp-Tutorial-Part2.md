---
layout: post
title: "微信小程序开发教程[Part 2]"
subtitle: "动手写你的第一个小程序"
date: 2017-04-12
author: LiamHsia
category: Tutorial
tags: 微信小程序 前端开发
finished: true
---
> 前端程序员是最为「贪婪」的，他们不满足前端开发，便弄出了Nodejs，不满足后端开发，便开始「染指」移动端。

我们把微信小程序类比成「自带后端逻辑」的网页栈是比较合适的，视图层是前端表现部分，逻辑层则包括网页的两个部分，网页显示逻辑和服务器交互逻辑，如果你懂一点MVC的话，逻辑层是包含Controller和Model两个部分，逻辑层一方面控制页面的渲染(C)，一方面将数据处理(M)。

![逻辑](http://ooyc2y4k2.bkt.clouddn.com/Sr38p)

## 文件结构

![文件结构](http://ooyc2y4k2.bkt.clouddn.com/NehEN)

打开我们的项目，微信小程序的结构和我们网页开发很是相像，`asset`文件夹用来放资源文件，`pages`用来放置页面文件，`utils`用来放一些工具文件。

除此之外，在根目录下还有有三个配置文件，功能如下：

![配置文件](http://ooyc2y4k2.bkt.clouddn.com/nhIuw)

### app.js
当小程序启动时，我们想注册哪些参数，进行哪些操作，我们在此文件中完成。
```javascript
let util = require('/utils/util.js')
App({
  openid: '',
  nickname: '',
  avatar_url: '',
  emojis: util.get_emojis()
})
```
因为Emoji表情我想在启动之时便注册到全局，以便后续页面使用，如何使用这个全局变量，我们后续再做说明。
```javascript
emojis: util.get_emojis()
```
### app.json

json文件不用想，那肯定是全局配置文件了，决定页面文件的路径、窗口表现、设置网络超时时间、设置多 tab 等。

```javascript
{
  "pages":[
    "pages/landing_page/index",
    "pages/home/index"
  ],
  "window":{
    "backgroundTextStyle":"light",
    "navigationBarBackgroundColor": "#fff",
    "navigationBarTitleText": "Emojiary",
    "navigationBarTextStyle":"black"
  }
}
```
用不到那么多配置，现在只用到`pages`和`window`两个配置项，`pages`用来表示整个小程序由多少个页面组成，默认第一条为启动页面，`window`用于设置小程序的状态栏、导航条、标题、窗口背景色，具体设置项的意义就不一一赘述了。

### apps.wxss

全局的样式，一些全局共用的样式可以写在这里。

## 页面
我们打开`pages`文件夹，可以发现，两个页面就是对应的我们两个目录，以启动页`landing_page`为例，来剖析整个代码。

![目录结构](http://ooyc2y4k2.bkt.clouddn.com/0F0VF)

### index.wxml & index.wxss

> WXML（WeiXin Markup Language）是框架设计的一套标签语言，结合基础组件、事件系统，可以构建出页面的结构。

>WXSS(WeiXin Style Sheets)是一套样式语言，用于描述 WXML 的组件样式。

通过官方描述我们可以知道，WXML和WXSS其实就是HTML和CSS的变种，WXML集成了组件和一些事件处理，而WXSS保持CSS大部分特性的同时，增加了样式导入，和尺寸单位以此来保证不同分辨率设备能够得到统一的显示效果。

和前端开发一样，我偏向于先用WXML和WXSS实现页面，至于逻辑处理则放到最后再实现。

![登陆页](http://ooyc2y4k2.bkt.clouddn.com/Dopi0)

### index.js
当我们实现完页面，就该考虑一下，需要什么的逻辑功能呢？我这个登陆页逻辑很简单：
- Emoji图片是随机的，加载时有动效
- 拿到微信用户的授权信息
- 跳转到主页面

在WXML中，标签我们称之为组件，`view`为最基本的组件：视图容器，你可以理解为，所有可视元素的载体，还有很多其他组件，比如我们要用到的展示Emoji图片的`image`组件。
```html
<view class="userinfo-box">
    <image class="userinfo-avatar ripple rotateIn" src="{{ emoji_url }}" background-size="cover"></image>
</view>
  ```
因为Emoji图片的地址是随机的，所以这个地址需要逻辑层处理完给我们，就用到了双向数据绑定，如果你看了`Vuejs`一定对此不会陌生。

好的，去`index.js`编写我们的这个逻辑。
```javascript
let app = getApp()
```
还记得我们在初始化`app.js`中注册的全局实例么？我们可以用上面的方法来取得这个全局实例。
```javascript
data: {
		emoji_url : '/asset/emojis/slightly-smiling-face.svg'
	},
```
双向数据绑定，视图层中`emoji_url`和这里的`emoji_url`是互相绑定的，视图层变量数据发生变化，同时逻辑层的变量值也会随之改变，反之亦然。这里，我给了Emoji图片一个默认的值，会在接下来的页面加载时进行改变。
```js
let emojis = app.emojis
let positive_emojis = emojis['positive']
let emoji_name = positive_emojis[Math.floor(Math.random() * positive_emojis.length)]
this.setData({
	emoji_url : '/asset/emojis/' + emoji_name + '.svg'
})
console.log(this.data.emoji_url)
```
第1行代码就是拿到全局实例中的`emojis`变量，第2，3行取得一个随机的表情名。第4行，我们借助`this.setData()`这个函数，来改变`emoji_url`的值，log一下该变量的值，没问题。

接下来的代码就是实现我们第二个逻辑：拿到用户的授权信息，这部分代码就不细讲了，如果你不知道此部分的内容，可以参考[微信公众平台相关文档](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316518&token=&lang=zh_CN)。

```js
complete: () => {
	// 一秒后跳转到主页面
	setTimeout(() => {
		console.log('jump')
		wx.redirectTo({
			url: '/pages/home/index'
		})
	}, 1000)
}                    
 ```
                    
获取到用户的授权信息后，跳转到主页面，大功告成，我们的小程序启动页就搞定了，很简单吧。

后面主页面代码，就不再做过多解释，毕竟该教程还只是一个偏向新手的初级教程，仔细阅读我主页面的代码(并不复杂)，和多看官方文档，很容易就上手了。

在下一部分，我会说一说微信小程序开发应该注意什么，哪些Trick，还有我对小程序的看法。