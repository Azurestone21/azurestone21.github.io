---
title: 【API】地理位置 getFuzzyLocation
date: 2022-09-03 17:55:30
category: [小程序]
tags: [微信小程序]
---

[https://developers.weixin.qq.com/miniprogram/dev/api/location/wx.getFuzzyLocation.html](https://developers.weixin.qq.com/miniprogram/dev/api/location/wx.getFuzzyLocation.html)

该 API 可以获取当前的模糊地理位置，但精度存在较大偏差，通常在 1 到 6 公里之间。适合用于获取当前城市，不适合获取具体位置。

## 开通接口

1. 登录微信开放平台
2. 管理 -》开放管理 -》接口设置
3. 找到`wx.getFuzzyLocation`开通

## 配置

<font style="color:rgb(34, 34, 34);">app.json</font>

```javascript
permission: {
  'scope.userFuzzyLocation': {
    desc: '您的位置信息将用于位置校验'
  }
},
requiredPrivateInfos: ['getFuzzyLocation'],
```

`getLocation`和`getFuzzyLocation`不能同时配置使用，不然会报错：

> requiredPrivateInfos 'getFuzzyLocation' is is mutually exclusive with 'getLocation'.

## 更改描述

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260308001118581.png)

在配置中设置`permission`的`desc`。

## 报错信息

| errMsg                                    | 原因                     |
| ----------------------------------------- | ------------------------ |
| getLocation:fail auth deny                | 没有打开小程序的定位权限 |
| getLocation:fail system permission denied | 没有打开系统的定位权限   |

## 模拟手机不开启位置

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260308001018434.png)

模拟操作 -》应用设置

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260308001018436.png)
