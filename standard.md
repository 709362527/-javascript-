# 前后端接口基本规范<br>
## 建立规范背景
随着公司开发需求越来越多元化，技术要求不断的深化；在实际工作中，前后端的接口联调对接工作量占比在30%-50%左右，不规范的接口调试时间占比会更高，为了尽量避免沟通联调产生的不必要的问题，不断加快开发效率和逻辑易读性而制定；

## 一、接口返回数据的基本格式

```
{
  code: 200,  //必须
  data: {   //必须
    id: 1,
    name: "XXX",
    codeId: "XXX" //注意：所有的命名都是以驼峰命名法
  },
  status:true,//非必，返回接口请求的状态
  message:"success!"  //非必
}
```
#### 1、code(Number),返回当前接口的请求处理状态
```
200: 请求处理成功
500: 请求处理失败
401: 请求未认证，跳转登录页
406: 请求未授权，跳转未授权提示页
```
#### 2、data(Json),返回当前接口的数据信息，如果code不为200，则返回空数组null<br>
#### 3、status([Boolean]),返回当前接口请求的状态，成功或失败<br>
#### 4、message([String]),返回当前接口的消息文字<br>

## 二、HTTP请求方式

```
常用的HTTP动词有下面四个（括号里是对应的SQL命令）。
GET（SELECT）：从服务器取出资源（一项或多项）。
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
DELETE（DELETE）：从服务器删除资源。
```

## 三、key值规范<br>

#### 1、完全通用的key值
````
（1）分页
    recordCount: 当前页记录数
    totalCount: 总记录数
    pageNo: 当前页码
    pageSize: 每页大小
    totalPage: 总页数
（2）时间条件
    currentDate：当前时间
    startDate:开始时间
    endDate：结束时间
（3）过滤条件
    sortby：排序列名
    orderBy：排序规则（asc、desc）
    producy_type：筛选条件
````
#### 2、公司内部通用的key值
````
（1）系统信息和身份信息
    ztOpenId：中天的openId
    openId:本系统的openId
    appId：系统的appId
    userName：真实姓名
    nickName：系统昵称
    userId：本系统的用户ID
    deptName：部门名称
（2）数据信息
    province：省（可以套用的关键词，例如provinceId代表省Id、provinceName代表省中文名，下面以此类推）
    managementArea：管理区/省区
    city：市
    district：区县
    center：中心
    site：网点
    staff：员工
````
## 四、value值规范
````
  1、非必填参数，无需传任何值包括key值，例如www.baidu.com?openId=  或者www.baidu.com?openId=null，都是完全没有必要的
  2、必填参数
    (1)空值""
    (2)逻辑值Boolean，1或0
    (3)禁止性规范：
        中文传参
        重复没必要的参数
````
## 时间仓促，会持续更新，欢迎指正和补充，感谢~~~
