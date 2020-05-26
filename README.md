## mini-programs-1
小程序实现新闻页面
## 知识点整理

小程序中没有DOM，通过数据绑定传递数据--单向数据绑定

### 各个文件解释

`page`文件夹中放页面

`app.json `全局配置文件

`wxml`类似于`html`文件，`wxss`同理

`<view>`基本等同于`<div>`


### 点击跳转页面

`wx.navigateTo `     `wx.switchTab`

`bind:tap`和`catch:tap`的区别：`catch`阻止冒泡

```javascript
//wxml中
<view class="journey-container" bindtap="onTap">
//js中
    // wx.navigateTo({
    //     url:"../posts/post"
    // });
wx.switchTab({
    url: "../posts/post"
});
```
### slider组件使用

```html
<swiper vertical="{{false}}" indicator-dots="true" autoplay="true" interval="5000">
    <swiper-item>
        <image src="/images/wx.png"></image>
    </swiper-item>
    <swiper-item>
        <image src="/images/vr.png"></image>
    </swiper-item>
    <swiper-item>
        <image src="/images/iqiyi.png" ></image>
    </swiper-item>
</swiper>
```
### 数据传递

在js文件中设置数据，setData里面要传js的对象

```javascript
Page({
  data: {
  },
  onLoad: function () {
    this.setData({
       postList:postsData.postList
      });
  }
}

在wxml文件中通过 {{ }} 绑定数据
```
### 列表渲染

`wx:for`

`item`是数组`postList`下的子元素,且item是默认名称，即使不设置也存在

`idx`数组下的子元素序号

```
<block wx:for="{{postList}}" wx:for-item="item" wx:for-index="idx">
        <!--//template-->
        <view catchtap="onPostTap" data-postId="{{item.postId}}">
             <template is="postItem" data="{{...item}}"/>
        </view>
</block>
```

### 条件渲染

是否显示取决于`false/true`

```javascript
wx:if="{{false/true}}"
wx:else
```

### 模板template

`template`是占位符，运行后这标签在控制台看不到

```###
<import src="xxx.wxml">   导入模板
<template is="postItem"> 使用模板
模板格式 :
<template name="postItem">
    <view></view>     
</template>

@import "xxx.wxss"导入模板样式
```

### 自定义属性

`data-*`

```html
<view catchtap="onPostTap" data-postId="{{item.postId}}">
    <template is="postItem" data="{{...item}}"/>
</view>


```

js里拿到这个`postId`，通过`？`传递给下个页面

```javascript
onPostTap: function (event) {
    var postId = event.currentTarget.dataset.postid;
    // console.log("on post id is" + postId);
    wx.navigateTo({
      url: "post-detail/post-detail?id=" + postId
    })
}
```

在`onload`中接受参数

```javascript
onLoad: function (options) {
    var postId = options.id;
    this.data.currentPostId = postId;
    var postData = postsData.postList[postId];
    this.setData({
    postData: postData
})
```

### 小程序缓存

```javascript
//读取缓存
var postsCollected = wx.getStorageSync('postsCollected')

//将123保存到postsCollected
wx.setStorageSync('postsCollected',123);

//删除缓存
wx.removeStorageSync('postsCollected')
```

### 消息提示框

```javascript
wx.showModal({
      title: "收藏",
      content: collected ? "是否取消收藏" : "是否收藏",
      showCancel: "true",
      cancelText: "取消",
      cancelColor: "#333",
      confirmText: "确认",
      confirmColor: "#405f80",
      success: function (res) {//判断是收藏还是取消
        if (res.confirm) {
          collected = !collected
          postsCollected[that.data.currentPostId] = collected
          wx.setStorageSync('postsCollected', postsCollected)
          that.setData({ collected })
        }
      }
    })
```

小程序提供很多交互API

具体操作参考官方文档

### Tabbar

需要在app.json全局配置文件中配置

```json
"tabBar": {
    "borderStyle": "white",
    "position": "bottom",
    "list": [
      {
        "pagePath": "pages/posts/post",
        "text": "阅读",
        "iconPath": "images/tab/yuedu.png",
        "selectedIconPath": "images/tab/yuedu_hl.png"
      },
      {
        "pagePath": "pages/posts/post",
        "text": "电影",
        "iconPath": "images/tab/yuedu.png",
        "selectedIconPath": "images/tab/yuedu_hl.png"
      }
    ]
  }
```



### 其他

监听事件不要在`template`标签和`block`上

调试技巧：选择编译模式可以指定单独编译哪个页面
