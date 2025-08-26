# 实验一：第一个微信小程序

## **一、实验目标**

1、学习使用快速启动模板创建小程序的方法；

2、学习不使用模板手动创建小程序的方法。



## 二、实验步骤

列出实验的关键步骤、代码解析、截图。

### （一）使用快速启动模版

新建小程序，目前不需要后端服务，选择不使用云服务。模版中选择JS-基础模板。

![image-20250825142121347](https://pic.ericzht.space/PicGo/image-20250825142121347.png)



### （二）不用模板手动创建

选择JS基础模板后在文件夹中将所有文件删除，重头开始编写。

#### 1、创建页面文件

- 全局需要三个文件：app.js，app.json，app.wxss 并且名字不可修改

- 创建用于存储页面的pages文件夹，创建用于展示封面的文件夹index，右键新建page，命名为index，会自动生成所需要的四个文件。
  - js：页面逻辑实现
  - json：负责标题栏和状态栏
  - wxml：管理页面中的元素
  - wxss：页面排版

<img src="C:\Users\19355\AppData\Roaming\Typora\typora-user-images\image-20250825151244654.png" alt="image-20250825151244654"  />

#### 2、创建元素

1. `<image></image>`:图片
2. `<text></text>`:文字
3. `<button></button>`:按钮



#### 3、修改元素样式

在wxss中进行修改。

1、为元素添加class属性后，在wxss中使用`.class名`来对对应的元素进行调整。

![image-20250825153626057](https://pic.ericzht.space/PicGo/image-20250825153626057.png)

![image-20250825153637234](https://pic.ericzht.space/PicGo/image-20250825153637234.png)

2、也可以直接使用`元素名`来直接进行调整

如：

```css
image{
    width:300rpx;
}
text{
    color: #014099;
}
```

#### 4、修改头部样式

![image-20250825164647200](https://pic.ericzht.space/PicGo/image-20250825164647200.png)

#### 5、逻辑实现

index.wxml:

```xml
<view class="container">
  <image src='{{src}}'></image>
  <text>{{name}}</text>
  <button bindtap="getProfile">点击获取头像和昵称</button>
</view>
```

index.js:

```js
  //获取用户信息
  getProfile: function(e) {
    //推荐使用wx.getuserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认，
    // 开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
    wx.getUserProfile({
      desc: '展示用户信息', // 声明获取用户个人信息后的用途，后续会展示在弹窗中
      success: (res) => {
        console.log(res)
        this.setData({
          src: res.userInfo.avatarUrl,
          name:res.userInfo.nickName,
        })
      }
    })
  } ,
```



## 三、程序运行结果

### （一）使用快速启动模板创建

<img src="https://pic.ericzht.space/PicGo/image-20250825142312186.png" alt="image-20250825142312186" style="zoom:50%;" /> <img src="https://pic.ericzht.space/PicGo/image-20250825170140857.png" alt="image-20250825170140857" style="zoom: 50%;" /> 





### （二）不用模板手动创建

<img src="https://pic.ericzht.space/PicGo/image-20250825173118959.png" alt="图1" style="zoom: 50%;" /> <img src="https://pic.ericzht.space/PicGo/image-20250825173136004.png" alt="图2" style="zoom: 50%;" />



## 四、问题总结与体会

**遇到的问题：**

由于微信接口改变，getUserInfo已无法获取用户信息。于是按照官方说明进行修改，用户需要点击头像和昵称分别进行确认才可使用用户信息。

<img src="https://pic.ericzht.space/PicGo/image-20250825163749571.png" alt="image-20250825163749571" style="zoom:50%;" /> <img src="https://pic.ericzht.space/PicGo/image-20250825163819713.png" alt="image-20250825163819713" style="zoom:50%;" /> <img src="https://pic.ericzht.space/PicGo/image-20250825165828376.png" alt="image-20250825165828376" style="zoom:50%;" />

（在本地设置中切换调用基础库版本，调整到较老的版本可以在开发工具中测试getUserProfile函数，但是无法在用户手机中使用。）

![image-20250825184607158](https://pic.ericzht.space/PicGo/image-20250825184607158.png)

**体会：**

这是初次尝试微信小程序，通过获取用户信息的这个功能的实现，学习了微信小程序的代码结构，以及css,html,js等语言的语法，体会到了前端的乐趣。
