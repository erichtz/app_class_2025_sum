# 实验3：微信小程序云开发



## **一、实验目标**

学习微信小程序云开发的基础知识。能够完成利用文本搜索的功能就好，图像识别、语音识别接口有时有问题，不强求。



## 二、实验步骤

### （一）注册百度智能云，创建图像识别应用，记录api key和secret key

[图像识别 - 百度智能云控制台](https://console.bce.baidu.com/ai/?_=&fromai=1#/ai/imagerecognition/app/create)

![image-20250901143214773](https://pic.ericzht.space/PicGo/image-20250901143214773.png)

### （二）导入小程序项目

![image-20250901143324536](https://pic.ericzht.space/PicGo/image-20250901143324536.png)



### （三）创建云开发环境

![image-20250901143728857](https://pic.ericzht.space/PicGo/image-20250901143728857.png)



### （四）将环境id，小程序appid，百度智能云api key和secret key分别填入相应位置

环境id：

![image-20250901144131565](https://pic.ericzht.space/PicGo/image-20250901144131565.png)

小程序appid：

![image-20250901144233012](https://pic.ericzht.space/PicGo/image-20250901144233012.png)

百度智能云api key和secret key：

![image-20250901144315142](https://pic.ericzht.space/PicGo/image-20250901144315142.png)



### （五）部署云函数

1、首先右键选择环境，将cloudfunction文件夹的云环境配置到之前创建的云环境中（cloud1）

![image-20250901144826386](https://pic.ericzht.space/PicGo/image-20250901144826386.png)

2、将cloudfunction文件夹中的函数，依次右键，上传并部署：云端安装依赖（不上传node_modules）

![image-20250901144926869](https://pic.ericzht.space/PicGo/image-20250901144926869.png)

### （六）部署云数据库

1、下载垃圾分类数据集.zip并解压

2、在云开发控制台中点击数据库，分别创建trash，type集合

3、依次导入trash.json，type.json

![image-20250901152544031](https://pic.ericzht.space/PicGo/image-20250901152544031.png)

## 三、程序运行结果

![image-20250901145254820](https://pic.ericzht.space/PicGo/image-20250901145254820.png)



## 四、问题总结与体会

1、在实验中一开始没有分清环境id，小程序id和百度智能云api的相关参数，导致填写了不对的appid产生报错。后来仔细区分了一下这几个参数，正确填写。

2、一开始由于教程中第五步没有将文件夹配置到云环境的步骤，直接右键上传并部署报错，说请先选择云环境。根据报错找到错误原因，右键先选择云环境，解决问题。



