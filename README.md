
> H5-小程序

## 框架 
	个人来说推荐wepy,需要ui的话可以加入WeUI或者minUI
## 主要功能点

1.微信授权登录及手机号&邮箱登录+注销
目前看微信这块取消授权，只能用户自己手动微信设置,不过这里可以引导用户去关闭 允许获取我的信息  方法：wx.openSetting()

2.用户实名认证
等待腾讯云api

3.查看签署文件，并签署

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
