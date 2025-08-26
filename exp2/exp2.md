# 实验二：天气查询小程序

（已隐藏api_host，api_key）

## **一、实验目标**

1、学习使用快速启动模板创建小程序的方法；2、学习不使用模板手动创建小程序的方法。



## 二、实验步骤

### （一）搭建文件结构

选择js基础模板，将文件夹中的内容全部删除。

新建app.js,app.json,app.wxss，并新建pages,images文件夹，新建index页面

### （二）创建并排版天气界面的元素

![image-20250826094016459](https://pic.ericzht.space/PicGo/image-20250826094016459.png)

页面整体样式设置：

```css
.container{
  height: 100vh; /* 将container占满整个页面 */
  display: flex; 
  flex-direction: column; /* 设置主轴的方向，column：主轴是垂直方向，起点在上 */
  align-items: center; /* 在交叉轴方向上的对齐方式，center：居中对齐 */
  justify-content: space-around; /* 主轴方向上的对齐方式，space-around：平均分布 */
}
```

天气详细内容的布局：

```html
<view class="detail">
    <view class='bar'>
      <view class='box'>湿度</view> 
      <view class='box'>气压</view> 
      <view class='box'>能见度</view> 
    </view>
    <view class='bar'>
      <view class='box'>0%</view> 
      <view class='box'>0hPa</view> 
      <view class='box'>0km</view> 
    </view>
    <view class='bar'>
      <view class='box'>温度</view> 
      <view class='box'>风速</view> 
      <view class='box'>风力</view> 
    </view>
    <view class='bar'>
      <view class='box'>0℃</view> 
      <view class='box'>0km/h</view> 
      <view class='box'>0级</view> 
    </view>
  </view>
```

一个bar为一行，共有四行。一行中使用三个box展示信息。

样式：

```css
.bar{
  display: flex;
  flex-direction: row; /* 主轴设置为水平方向 */
  margin: 20rpx 0; /* 设置上下外边距各为20rpx，左右外边距为0（简写方式） */
}

.box{
  width: 33.3%;
  text-align: center; /* 文字排列在box容器的中央 */
}
```

margin属性的详细写法为：margin-top`, `margin-right`, `margin-bottom`, `margin-left

进一步样式优化：

<img src="https://pic.ericzht.space/PicGo/image-20250826131944821.png" alt="image-20250826131944821" style="zoom:50%;" />



### （三）更新省、市、区的数据

1、地区选择器中的内容修改为{{region}}参数，并且绑定函数changeRegion，用于监听选项变化，一旦变化则调用该函数。

```html
    <picker mode = 'region' bindchange="changeRegion">
        <view>{{region}}</view>
    </picker>
```

2、js文件中的初始值调整为含有省、市、区的数组。创建changeRegion函数，现修改显示的内容

```js
data: {
    region:['北京市','北京市','东城区'],
    locationID:101010100,
    now:{
        temp:0,
        text:'未知',
        icon:'999',
        humidity:0,
        pressure:0,
        vis:0,
        windDir:0,
        windSpeed:0,
        windScale:0
    }
  },
},
changeRegion: function(e){
    this.setData({
      region:e.detail.value
    })
}
```

### （四）获取实时天气数据

1、注册和风天气开发者账号

2、创建项目、创建凭据，并获得API key

![image-20250826132828993](https://pic.ericzht.space/PicGo/image-20250826132828993.png)

3、使用https://your_api_host/v7/weather/now?location=101010100&key=your_key在浏览器中进行测试，看是否能正确获取到信息

![image-20250826133023267](https://pic.ericzht.space/PicGo/image-20250826133023267.png)

获取成功

4、由于和风天气api接口发生变化，location必须传入地址的locationId或者是经纬度，不能使用地址名称，所以还需要使用和风天气提供的地址转locationId函数 （已隐藏api_host和api_key）

```js
  getlocationID: function(){
      var that = this;
      return new Promise(resolve => {
        wx.request({
          url: 'https://your_api_host/geo/v2/city/lookup?',
          data:{
              location:that.data.region[1],
              adm:that.data.region[0],
              key:'your_key' 
          },
          success: res => {
              console.log(res.data);
              that.setData({locationID:res.data.location[0].id})
              return resolve();
          },
        })
      });
  },
```

5、通过locationId获取天气信息

```js
  getWeather:function(){
    var that=this; // var 全局，this局部
    wx.request({
      url: 'https://your_api_host/v7/weather/now?',
      data:{
        location:that.data.locationID,
        key:'your_key'
      },
      success:function(res){
        console.log(res.data)
        that.setData({now: res.data.now})
      }
    })
  },
```

**注：由于let声明的变量是块级作用域，只在当前代码块中可用。var声明的变量是全局作用域，并且this也是块级作用域，无法传递给微信的接口，因此需要声明全局作用域的that。**

6、添加选择器触发的changeRegion函数

```js
  changeRegion: function(e){
    this.setData({
      region:e.detail.value // 将显示的提取调整为新的地区
    })
    this.getlocationID().then(result => { // 先获取locationId，再将locationId传入getWeather获取天气信息
      this.getWeather(); 
    })
  },
```



### （五）更新页面天气信息

1、将页面中动态的元素全部替换为变量

```html
<view class = 'container'>
    <!--区域1：地区选择器-->
    <picker mode = 'region' bindchange="changeRegion">
        <view>{{region}}</view>
    </picker>
    <!--区域2：单行天气信息-->
    <text>{{now.temp}}℃ {{now.text}}</text>
    <!--区域3：天气图标-->
    <image src = '/images/{{now.icon}}.svg' mode = 'widthFix'></image>
    <!--区域4：多行天气信息-->
    <view class = 'detail'>
        <view class = 'bar'>
            <view class = 'box'>湿度</view>
            <view class = 'box'>气压</view>
            <view class = 'box'>能见度</view>
        </view>
        <view class = 'bar'>
            <view class = 'box'>{{now.humidity}} %</view>
            <view class = 'box'>{{now.pressure}} hPa</view>
            <view class = 'box'>{{now.vis}} km</view>
        </view>
        <view class = 'bar'>
            <view class = 'box'>风向</view>
            <view class = 'box'>风速</view>
            <view class = 'box'>风力</view>
        </view>
        <view class = 'bar'>
            <view class = 'box'>{{now.windDir}} </view>
            <view class = 'box'>{{now.windSpeed}} km/h</view>
            <view class = 'box'>{{now.windScale}} 级</view>
        </view>
    </view>
</view>>
```

## 三、程序运行结果

<img src="https://pic.ericzht.space/PicGo/image-20250826135052400.png" alt="image-20250826135052400" style="zoom: 50%;" /> <img src="https://pic.ericzht.space/PicGo/image-20250826135105015.png" alt="image-20250826135105015" style="zoom: 50%;" />

## 四、问题总结与体会

**遇到的问题：**

通过地址名称无法获取到对应的天气数据。

**解决方法：**

浏览了和风天气的官方文档后发现，接口方法已经修改了，无法通过地址名称去获取天气，而是必须通过经纬度或者locationId获取。学习官方文档地址名称转locationId的api的使用方法后进行转换。

**体会：**

在使用第三方api的时候，在遇到很多问题，比如接口更新而无法使用时，多看官方文档可以解决很多问题。