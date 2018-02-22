
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
# 小程序V1.0 后端技术方案 
[TOC]

主要流程:用户打开工商(或e签宝)小程序,用户主动点击授权提供用户信息,创建一条不做任何关联的用户数据,通过腾讯云实名后,关联sign_job用户体系(姓名,证件号,证件类型)和快捷签账号(projectId,姓名,身份证).
用户实名后,可以从sign_job拉去从工商发起的,对应userid的签署作业完成签署.

# 前端设计

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fo6l4f0brtj31ia4xv4qp.jpg)

#1. 后端设计 
## 1.1 整体架构
微信应用(wechat-treaty) :小程序客户端展示页面,和后端进行逻辑交互.
标准签应用(esign-treaty) 拆分前的主要核心逻辑所处服务.
通用服务(...) 提供(待我签,已完成列表,签署,详情)等通用接口所处服务.
实名认证服务(...) 此次需求主要做腾讯云实名认证相关业务 提交实名信息 获取实名结果. 它和第三方系统交互.

## 1.2. restful 接口规范 
   根据itemId查询资源详情： GET https://api.example.com/resource/itemId 
 
<!-- more --><!-- more --><!-- more -->==<!-- more --><!-- more -->====== 
  创建某资源： POST https://api.example.com/resource/ CONTENT:{"key":"vale"} 
 
  根据itemId更新某资源 PUT https://api.example.com/resource/itemId CONTENT:{"key":"vale"} 
 
  根据itemId删除某资源 Delete https://api.example.com/resource/itemId 
 
### 1.3. 异常（错误）规范
``` java
@ApiModel("通用结果模型")
public class Result<Data> implements Serializable {
    @ApiModelProperty("状态 0-表示成功 其他数字表示错误")
    private Integer status;
    @ApiModelProperty("消息")
    private String message;
    @ApiModelProperty("数据")
    private Data data;
    @ApiModelProperty("响应时间, 日期格式:yyyy-MM-dd HH:mm:ss.S")
    @JsonFormat(
        pattern = "yyyy-MM-dd HH:mm:ss.S",
        timezone = "GMT+8"
    )

```

### 1.4. 日志记录(业务接口)规范

### 1.5. 整体数据库模型设计 

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fo6kbnktoyj31kw130n7n.jpg)
### 1.6. 整体流程图设计

![](https://ws4.sinaimg.cn/large/006tKfTcgy1fo5j8wgehaj30uk0oydi3.jpg)

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fo6kcqqulvj31kw12mk6a.jpg)

### 1.6.1 实名认证 
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fo6pdwzcvuj31kw1qutj3.jpg)


## 2. 主要功能概述
#### 2.1 微信用户登录(非登录后端账号) 后端缓存 key:自定义3rd_session value:session_openId
#### 2.2 创建后端账号
#### 2.3 实名账号 创建或关联sign-job用户和快捷签账号
#### 2.4 展示签署列表  工商小程序只有工商发起的工商待我签,待他人签,已完成列表.
#### 2.5 查询详情
#### 2.6 签署




## 3 接口文档


##[微信相关API]

文档信息
https://mp.weixin.qq.com/debug/wxadoc/dev/api/open.html

##[后端业务API]


### code2session

* **请求URL**

>https://IP:post/service/rest/v1/wechat/code2session

*  **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|source|  String| 来源|
|code|String|code|


- **返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|thirdSession|String|session|
|needLogin|boolean|微信是否已经绑定了账号|
|authStatus|Integer|如果needLogin=false 1 实名 0-未实名|

* 20001 登录微信失败

- **返回示例**

```
{
  "data": {
   
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```


### 直接通过手机号直接创建账号

不同小程序openId和uId不同 

* **请求URL**

>https://IP:post/service/rest/v1/account/create

*  **请求方式** 

>**POST**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|source|  String| 来源|
|encryptedData|String ||
|iv|String ||
|thirdSession|String|会话|


- **返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|id|  String| 用户id|
|mobile|String |用户手机号|
|authStatus|int |用户认证状态|

* 20002 会话失效 重新登录微信

- **返回示例**

```
{
  "data": {
   
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```


### 获取指定的 用户相关信息


* **请求URL**

>https://ip:port/service/rest/v1/account/userInfo

*  **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
| source|  String| wechat|
|thirdSession|String|session|


- **主要返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|id|String|wechatUserId|
|mobile|String|手机号|
|authStatus|int|用户认证状态|

  

- **返回示例**


```
{
  "data": {
   
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```
错误代码

>| 错误代码 | 返回msg | 详细描述 |
|:-------------:|:-------------|


### 待我签,待他人签,已完成列表获取 分页查询

* **请求URL**

>https://IP:post/service/rest/v1/signflow/list

*  **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
| source|  String| 来源|
|thirdSession|String|session|
| page|  int| 当前页|
| pageSize|  int| 列表数|
| status|  int| 类型(待我签-2)(待他人签-3)(已处理-9)|

- **返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |

- **返回示例**

```
{
 "data" : {
    "signFlows" : [
      {
        "docName" : "H5签署test",
        "status" : 2,
        "modifyDate" : 1511850537000,
        "evidenceId" : null,
        "payMethod" : 0,
        "isRead" : 1,
        "isRealSign" : 1,
        "senderName" : "王慧",
        "senderId" : "0A6417F5665A40DABB8D54B849CBCCC9",
        "isOwner" : 0,
        "lastOperation" : null,
        "flowDetailId" : null,
        "flowType" : 3,
        "flowId" : "3cbd3857-bcbf-46af-bff5-3995b86046b4",
        "deadline" : null,
        "participantId" : "f57f5d7a-b001-4790-b098-b00eb96b980e",
        "isApplication" : 0,
        "createTime" : 1511850464000,
        "payAccountInfo" : "0A6417F5665A40DABB8D54B849CBCCC9"
      },{...}
    ],
    "pageSize" : 10,
    "total" : 5,
    "page" : 1
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```

错误代码

>| 错误代码 | 返回msg | 详细描述 |
|:-------------:|:-------------|




### 签署任务内容详情


* **请求URL**

>https://ip:port/service/rest/v1/signflow/detail

*  **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
| source|  String| 来源|
|thirdSession|String|session|
| participantId|  int| 参与人id|

- **返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|下面是标准签返回 参考 [不返回url]

- **返回示例**


```
{
"data" : {
    "ownerId" : "3B62739ACB454947AF6F6758DB5B70B5",
    "payAccountName" : "马骏",
    "flowDocDataList" : [
      {
        "ossKey" : "e6f3eab2-685c-4464-a9a7-2a7d5d1404b1",
        "page" : 1,
        "flowDocName" : "20171212-template",
        "posList" : [
          {
            "posX" : 124,
            "posPage" : 1,
            "posY" : 250.82741999999999,
            "key" : "",
            "signType" : 0
          }
        ],
        "filetype" : "pdf",
        "flowDocId" : "1076ded3-b389-4d7b-b973-08346da879f0",
        "userFileId" : "19e12f86-8f1e-41c8-9c17-49b8978f93fb",
        "reviewUrl" : "https:\/\/esign-oss-release.oss-cn-hangzhou.aliyuncs.com\/pdf2Image\/UvpjE0U_UPvhFOhh_jqXqjN89kqHeo6Xfx34RoU1Irg%3D_1_1_50?Expires=1517137172&OSSAccessKeyId=FBzUaPMorqiiUAfb&Signature=Z23hO%2BGY4e06zaItJeWCIto2YiE%3D",
<!--    -----    "url" : "https:\/\/esign-oss-release.oss-cn-hangzhou.aliyuncs.com\/e6f3eab2-685c-4464-a9a7-2a7d5d1404b1?Expires=1517169272&OSSAccessKeyId=FBzUaPMorqiiUAfb&Signature=su0MW3HM6GpXjJam0QDjU8aeXG4%3D"-->
      }
    ],
    "status" : 2,
    "docName" : "111",
    "evidenceId" : null,
    "owner" : "马骏",
    "flowSealDataList" : [],
    "payMethod" : 0,
    "isReadOnly" : 0,
    "isRealSign" : 1,
    "isOwner" : 1,
    "rejecter" : null,
    "lastRemindDate" : null,
    "flowType" : 3,
    "flowId" : "f7738be7-5072-410b-8b21-ec13d1591832",
    "isOldVersionData" : 0,
    "deadline" : null,
    "participantId" : "b0cf714b-c529-4379-a2b1-e5ef6e7e4eca",
    "reason" : null,
    "isApplication" : 0,
    "createTime" : 1514311931000,
    "payAccountInfo" : "3B62739ACB454947AF6F6758DB5B70B5",
    "operationLogList" : null,
    "description" : ""
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```
错误代码

>| 错误代码 | 返回msg | 详细描述 |
|:-------------:|:-------------|




### 获取合同详情中的图片地址


* **请求URL**

>https://ip:port/service/rest/v1/signflow/getImageUrl

*  **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
| source|  String| 来源|
|3rd_session|String|session|
|flowDocId|String|文档id|
|pageNums|String|1 第1页  1-5 ,1-5页|


- **返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|docImageUrlLists|||

> | docImageUrlLists     |    |     |
| :-------- | :--------| :------ |
|url|String|图片地址|
|page|页面数||




### 发送短信


* **请求URL**

>http://ip:port/service/rest/v1/message/sendSms

*  **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|source|  String| wechat|
|thirdSession|String|session|
|type|int|1-签署短信意愿认证  **-快速登录/注册|


- **主要返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
 

- **返回示例**


```
{
  "data": {
   
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```
错误代码

>| 错误代码 | 返回msg | 详细描述 |
|:-------------:|:-------------|




### 签署


* **请求URL**

>https://ip:port/service/rest/v1/signflow/sign

*  **请求方式** 

>**POST**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|source|  String| 来源|
|thirdSession|String|session|
|code|String|加密后的值 短信验证码/签署密码/刷脸serviceId|
|validateType|int| 0- 提交验证码签署  1-提交签署密码签署 3-刷脸意愿认证|
|*docSignInfoList*|对象体|签署合同文档集合|

> | **docSignInfoList**      |     |     |
| :-------- | :--------| :------ |
| flowDocId|  String| 合同文档id|
|*posArray*|对象体|每份合同文档中落章位置|

> | **posArray**      |     |     |
| :-------- | :--------| :------ |
|posPage|  int| 落章的页码|
|posX|double|落章位置|
|posY|double|落章位置|
|sealBase64|String|如果是手绘 手绘base64|
|sealId|String|如果是盖章 印章id|
|participantId|String|参与人id|


- **主要返回参数**

> | 返回参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |



- **返回示例**


```
{
  "data": {
   
  },
  "message": "string",
  "respondTime": "2018-02-06T03:40:13.126Z",
  "status": 0
}
```
错误代码

>| 错误代码 | 返回msg | 详细描述 |
|:-------------:|:-------------|



### 身份证ocr

* **请求URL**

>https://IP:port/service/rest/v1/ocr/idcard

*  **请求方式** 

>**POST**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|source|  String| 来源|
|thirdSession|String|会话|
|imageKey|String|身份证照片OSS Key|



- **返回参数**

> | 返回参数      |     参数类型 |参数说明|
| :-------- | :--------| :------ |
|errCode|int|错误码|
|message|String|错误描述|
|serviceId|String|实名认证业务编号|
|data|Object| 身份证信息|



- **返回示例**

```
{
  "errCode" : 0,
  "message" : "成功",
  "serviceId" : "XXXXXX",
  "data" : {	
  		"name": "某某",
       "sex": "男",
       "nation": "汉",
       "birth": "2000/1/1",
       "address": "某地",
       "id": "123456200001011234"
   }
}
```

### 唇语验证码

* **请求URL**

>https://IP:port/realname/external/person/face/livegetfour

* **请求方式** 

>**GET**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|serviceId|String|实名认证业务id|

- **返回参数**

> | 返回参数      |     参数类型 |参数说明|
| :-------- | :--------| :------ |
|errCode|int|错误码|
|msg|String|错误描述|
|validate_data|String|验证码|



- **返回示例**

```
{
  "errCode" : 0,
  "msg" : "成功",
  "validate_data" : "8032"
}
```

### 唇语活体检测

* **请求URL**

>https://IP:port/service/rest/v1/face/idcardlivedetectfour

* **请求方式** 

>**POST**

- **请求参数**

> | 请求参数      |     参数类型 |   参数说明   |
| :-------- | :--------| :------ |
|source|  String| 来源|
|thirdSession|String|会话|
|video_osskey|String|视频osskey|
|validate_data|String|唇语验证码|
|serviceId|String|实名业务id|

- **返回参数**

> | 返回参数      |     参数类型 |参数说明|
| :-------- | :--------| :------ |
|errCode|int|错误码|
|msg|String|错误描述|



- **返回示例**

```
{
  "errCode" : 0,
  "msg" : "成功"
}
```

