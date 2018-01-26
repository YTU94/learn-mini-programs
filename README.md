
> H5-小程序

## 框架 
	个人来说推荐wepy,需要ui的话可以加入WeUI或者minUI
	
## 主要功能点

### 1.微信授权登录及手机号&邮箱登录+注销
微信授权获取手机号码：1.需要先调用wx.login，让后拿sessionKey还有appId,再去调用微信的解密算法，才拿到手机号
微信有自带的session,不过感觉还是要自己做，微信推荐自己派发一个 session 登录态
目前看微信这块取消授权，只能用户自己手动微信设置,不过这里可以引导用户去关闭 允许获取我的信息  方法：wx.openSetting()

### 2.用户实名认证
第一步身份证OCCR,需要兼顾扫和长传照片
人脸识别的接口
（等待腾讯云api）

### 3.查看签署文件，并签署
签署后分三种确认方式：1. 意愿认证，需要腾讯云活体检测，还要对比是否是本人
	       	    2. 签署弥漫签署
		    3. 短信验证码签署
是否添加设置位置签署

### 4.分享功能
待定中
微信有自带的转发功能，可以自定义标题，路径，图片url的，类似微信的分享

## 基础架构

计划用wepy或者直接原生的

以功能点分模块实现,tabBar采用两栏设计--文件，我的

## 框架结构
- dist wepy编译目录（最好不用直接修改）
- src 
  - components 公用组件
  - mixins 混合数据
  - pages 所有页面
    - index 主页
    - login 登录页
    - personal 个人页
  - app 小程序主页入口

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report

# run all tests
npm test
```
