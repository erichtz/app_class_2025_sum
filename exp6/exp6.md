# 实验6：推箱子游戏

## **一、实验目标**

1、综合所学知识创建完整的推箱子游戏；

2、能够在开发过程中熟练掌握真机预览、调试等操作。



## 二、实验步骤

### （一）创建页面文件

在pages文件夹中创建index，game页面。

### （二）创建images、utils文件夹

将图片保存在images文件夹中，在utils中添加data.js

<img src="https://pic.ericzht.space/PicGo/image-20250908162931121.png" alt="image-20250908162931121" style="zoom:50%;" />

### （三）导航栏设计

```json
{
  "pages": [
    "pages/index/index",
    "pages/game/game"
  ],
  "window": {
    "navigationBarBackgroundColor": "#E64340",
    "navigationBarTitleText": "推箱子游戏"
  }
}
```

<img src="C:\Users\19355\AppData\Roaming\Typora\typora-user-images\image-20250908163633028.png" alt="image-20250908163633028" style="zoom:50%;" />

### （四）页面设计

1、公共样式设计

```css
.container{
  height: 100vh;
  color: #E64340;
  font-weight: bold;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
}
.title{
  font-size: 18pt;
}
```

2、首页设计

```html
<view class="container">
  <view class="title">游戏选关</view>
  <view class='box'>
    <image src="/images/level01.png" mode=""/>
    <text>第 1 关</text>
  </view>
</view>
```

```css
/* pages/index/index.wxss */
.levelBox{
  width: 100%;
}

.box{
  width: 50%;
  float: left;
  margin: 20rpx 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

image{
  width: 300rpx;
  height: 300rpx;
}
```

3、游戏页面设计

需要用户点击首页的关卡，并在新窗口打开该页面。由于暂时没有做点击跳转的逻辑，所以在开发工具顶端选择普通编译-添加编译模式，携带临时测试参数`level=0`

![image-20250908165011264](https://pic.ericzht.space/PicGo/image-20250908165011264.png)

```html
<!--pages/game/game.wxml-->
<view class="container">
  <view class="title">第 1 关</view>
  <canvas canvas-id="myCanvas"/>
  <view class="btnBox">
    <button type="warn">↑</button>
    <view>
      <button type='warn'>←</button>
      <button type='warn'>↓</button>
      <button type='warn'>→</button>
    </view>
  </view>
  <button type="warn">重新开始</button>
</view>
```

```css
/* pages/game/game.wxss */
canvas{
  border:1rpx solid;
  width:320px;
  height:320px;
}

/*方向键按钮整体区域*/
.btnBox{
  display:flex;
  flex-direction:column;
  align-items:center;
}

/*方向键按钮第二行*/
.btnBox view{
  display:flex;
  flex-direction:row;
}

/*所有方向键按钮*/
.btnBox button{
  width:90rpx;
  height:90rpx;
}

/*所有按钮样式*/
button{
  margin:10rpx;
}

```

### （五）逻辑实现

#### 1、公共逻辑

地图数据：以二维数组形式存放。每个位置的数字代表对应的图标素材。

```js
// 1为墙，2为路，3为终点，4为箱子，5为任务，0为墙的外围
var map1 = [
  [0,1,1,1,1,1,0,0],
  [0,1,2,2,1,1,1,0],
  [0,1,5,4,2,2,1,0],
  [1,1,1,2,1,2,1,1],
  [1,3,1,2,1,2,2,1],
  [1,3,4,2,2,1,2,1],
  [1,3,2,2,2,4,2,1],
  [1,1,1,1,1,1,1,1]
]

var map2 = [
  [0,0,1,1,1,0,0,0],
  [0,0,1,3,1,0,0,0],
  [0,0,1,2,1,1,1,1],
  [1,1,1,4,2,4,3,1],
  [1,3,2,4,5,1,1,1],
  [1,1,1,1,4,1,0,0],
  [0,0,0,1,3,1,0,0],
  [0,0,0,1,1,1,0,0]
]

var map3 = [
  [0,0,1,1,1,1,0,0],
  [0,0,1,3,3,1,0,0],
  [0,1,1,2,3,1,1,0],
  [0,1,2,2,4,3,1,0],
  [1,1,2,2,5,4,1,1],
  [1,2,2,1,4,4,2,1],
  [1,2,2,2,2,2,2,1],
  [1,1,1,1,1,1,1,1]
]

var map4 = [
  [0,1,1,1,1,1,1,0],
  [0,1,3,2,3,3,1,0],
  [0,1,3,2,4,3,1,0],
  [1,1,1,2,2,4,1,1],
  [1,2,4,2,2,4,2,1],
  [1,2,1,4,1,1,2,1],
  [1,2,2,2,5,2,2,1],
  [1,1,1,1,1,1,1,1]
]
```

使用module.exports暴露数据接口

```js
module.exports = {
  maps:[map1,map2,map3,map4]
}
```

在game页面的JS文件顶端引用公共JS文件

```js
var data = require('../../utils/data')
```

#### 2、首页逻辑

##### （1）关卡列表展示

在首页的js文件中添加关卡图片的信息

```js
  data: {
    levels:[
      'level01.png',
      'level02.png',
      'level03.png',
      'level04.png'
    ]
  },
```

使用循环展示关卡列表数据和图片

```html
<view class="container">
  <view class="title">游戏选关</view>
  <view class='levelBox'>
    <view class='box' wx:for = '{{levels}}' wx:key='levels{{index}}'>
      <image src="/images/{{item}}" mode=""/>
      <text>第 {{index+1}} 关</text>
    </view>
  </view>
</view>
```

##### （2）点击跳转游戏页面

为关卡图片绑定点击事件，并在对应的`index.js`中添加`chooseLevel`函数的内容

```html
<view class="container">
  <view class="title">游戏选关</view>
  <view class='levelBox'>
    <view class='box' wx:for = '{{levels}}' wx:key='levels{{index}}' bindtap="chooeseLevel" data-level="{{index}}">
      <image src="/images/{{item}}" mode=""/>
      <text>第 {{index+1}} 关</text>
    </view>
  </view>

</view>
```

```js
  chooseLevel:function(e){
    let level = e.currrentTarget.dataset.level
    wx.navigateTo({
      url:'../game/game?level=' + level
    })
  },
```

#### 3、游戏页逻辑

##### （1）显示当前第几关

游戏页面接收首页跳转时携带的关卡信息，并显示对应的图片内容。

```js
  data: {
    level:1
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad(options) {
    let level = options.level
    this.setData({
      level:parseInt(level)+1
    })
  },
```

```html
<view class="title">第 {{level}} 关</view>
```

##### （2）游戏逻辑实现

游戏初始信息：

```js
var map = [
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0]
]
var box = [
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0],
  [0,0,0,0,0,0,0,0]
]
// 方块的宽度
var w = 40
//初始化游戏角色的行、列
var row = 0
var col = 0
```

初始化游戏画面：

​	根据当前是第几关读取对应的游戏地图信息，并更新到游戏初始数据中。

```js
  initMap: function(level){
    let mapData = data.maps[level]
    for(var i=0;i<8; i++){
      for(varj=0;j<8;j++){
        box[i][j]= 0
        map[i][j]= mapData[i][j]
        if(mapData[i][j] == 4){
          box[i][j]= 4
          map[i][j]= 2
        } else if(mapData[i][j]==5){
          map[i][j]= 2
          row = i
          col = j
        }
      }
    }
  },
```

先从公共函数文件data.js中读取对应关卡的游戏地图数据，然后使用循环对每一块地图数据进行解析，并更新当前游戏的初始地图数据、箱子数据以及玩家所在位置。

然后在game.js中添加自定义函数drawCanvas，用于将地图信息绘制到画布上。

```js
  drawCanvas:function(){
    let ctx = this.ctx
    ctx.clearRect(0,0,320,320)
    //使用双重for循环绘制8x8的地图
    for(var i=0;i<8;i++){
      for(var j=0;j<8;j++){
        //默认是道路
        let img =ice'
        if(map[i][j]==1){
          img ='stone'
        }else if(map[i][j]== 3){
          img = 'pig'
        }
        ctx.drawImage('/images/icons/'+img+'.png',j*w,i*w,w,w)
          if(box[i][j]== 4){
            //叠加绘制箱子
          ctx.drawImage('/images/icons/box.png',j*w,i*w,w,w)
          }
        }
      }
    //叠加绘制小鸟
    ctx.drawImage('/images/icons/bird.png',col*w,row*w,w,w)
    ctx.draw()
  },
```

在game.js的onLoad函数中创建画布上下文，并依次调用自定义函数initMap和drawCanvas。

```js
  onLoad(options) {
    let level = options.level
    this.setData({
      level:parseInt(level)+1
    })
    this.ctx = wx.createCanvasContext('myCanvas')
    this.initMap(level)
    this.drawCanvas()
  },
```

##### （3）方向键逻辑实现

为方向键绑定点击事件：

```html
  <view class="btnBox">
    <button type="warn" bindtap='up'>↑</button>
    <view>
      <button type='warn' bindtap='left'>←</button>
      <button type='warn' bindtap='down'>↓</button>
      <button type='warn' bindtap='right'>→</button>
    </view>
  </view>
```

分别实现上下左右的移动：

```js
  up:function(){
    if(row>0){
      if(map[row-1][col]!=1&&box[row-1][col]!=4){
        row=row-1
      }
      else if(box[row-1][col]==4){
        if(row-1>0){
          if(map[row-2][col]!=1&&box[row-2][col]!=4){
            box[row-2][col]=4
            box[row-1][col]=0
            row = row-1
          }
        }
      }
      this.drawCanvas()
    }
  },
  down:function(){
    if(row<7){
      if(map[row+1][col]!=1&&box[row+1][col]!=4){
        row=row+1
      }
      else if(box[row+1][col]==4){
        if(row+1<7){
          if(map[row+2][col]!=1&&box[row+2][col]!=4){
            box[row+2][col]=4
            box[row+1][col]=0
            row = row+1
          }
        }
      }
      this.drawCanvas()
    }
  },
  left:function(){
    if(row>0){
      if(map[row][col-1]!=1&&box[row][col-1]!=4){
        col=col-1
      }
      else if(box[row][col-1]==4){
        if(col-1>0){
          if(map[row][col-2]!=1&&box[row][col-2]!=4){
            box[row][col-2]=4
            box[row][col-1]=0
            col=col-1
          }
        }
      }
      this.drawCanvas()
    }
  },
  right:function(){
    if(col < 7){
      if(map[row][col+1] != 1 && box[row][col+1] != 4){
        col = col + 1
      }
      else if(box[row][col+1] == 4){
        if(col + 1 < 7){
          if(map[row][col+2] != 1 && box[row][col+2] != 4){
            box[row][col+2] = 4
            box[row][col+1] = 0
            col = col + 1
          }
        }
      }
      this.drawCanvas()
    }
  },
```

##### （4）判断游戏是否成功

```js
  isWin:function(){
    for(var i=0;i<8;i++){
      for(var j=0;i<8;j++){
        if(box[i][j]== 4 && map[i][j]!= 3){
          return false
        }
      }
    }
    return true
  },
```

游戏成功处理：一旦成功就弹出提示对话框

```js
  checkWin:function(){
    if(this.isWin()){
      wx.shaoModal({
        title:'恭喜',
        content:'游戏成功',
        showCancel:false
      })
    }
  },
```

添加游戏是否成功判断：

<img src="https://pic.ericzht.space/PicGo/image-20250908211614399.png" alt="image-20250908211614399" style="zoom:50%;" />

##### （5）重新开始游戏

给重新开始按钮绑定restartGame函数

game.js中添加对应函数

```js
  restartGame:function(){
    this.initMap(this.data.level-1)
    this.drawCanvas()
  },
```



## 三、程序运行结果

<img src="https://pic.ericzht.space/PicGo/image-20250908215041663.png" alt="image-20250908215041663" style="zoom:50%;" /><img src="https://pic.ericzht.space/PicGo/image-20250908215013792.png" alt="image-20250908215013792" style="zoom:50%;" /> <img src="https://pic.ericzht.space/PicGo/image-20250908214834837.png" style="zoom:50%;" /> 



## 四、问题总结与体会

本次实验我通过推箱子游戏的制作掌握了真机预览、调试的方法，学习了用二维数组存储地图，以及上下左右移动的方法。其中遇到上下左右按钮调试时无法使用的问题，输给ai进行排查，发现是有两个按钮的if条件判断中的条件在复制后没有对应方向的修改。ai在用于代码纠错时可以快速地帮助我们定位存在的问题。
