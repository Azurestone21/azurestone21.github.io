---
title: Jenkins接入钉钉机器人
date: 2026-03-17 11:31:55
categories: [服务器]
tags: [Jenkins, 钉钉机器人]
---

## 测试环境

- Jenkins 版本为 2.504.3

## 配置钉钉机器人

1. 进入钉钉群聊，进入"群设置 -> 群管理 -> 机器人"。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234228193.png)

2. 点击"添加机器人"，选择"自定义机器人"。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234230881.png)

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234235190.png)

1. 自定义“机器人名字”。设置“安全设置”，选择“IP地址(段)”，填入**服务器公网IP**。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234255148.png)

4. 点击"完成"。完成后，会显示 Webhook，Webhook需要配置到Jenkins中。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234257615.png)

## 配置Jenkins

### 方式1：安装钉钉插件

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234618791.png)

安装好钉钉插件后，在 Jenkins 首页就会出现"钉钉"选项卡，也可以在"系统管理 -> 系统配置"中找到"钉钉"选项卡。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234640603.png)

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234643130.png)

1. 进入"钉钉"选项卡，看到“机器人”，点击“新增”。
2. 填写id、名称、Webhook。
3. 点击“测试”，如果显示“测试成功”，钉钉群聊会收到一条消息，说明配置成功。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234705030.png)

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234738213.png)

4. 进入任务，配置/Configuration -> General -> 钉钉机器人，选择配置好的机器人，点击“保存”。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234716841.png)

5. 点击“立即构建”，查看钉钉群聊是否收到部署成功的通知。

#### 自定义消息内容

Jenkins 内置的环境变量：

- EXECUTOR_NAME：构建人姓名
- EXECUTOR_MOBILE：构建人手机号
- PROJECT_NAME：项目名称
- PROJECT_URL：项目地址
- JOB_NAME：任务名称
- JOB_URL：任务地址
- JOB_DURATION：任务持续时间
- JOB_STATUS：任务状态

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260318003605572.png)

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260318003937847.png)

### 方式2：在 执行 shell 脚本 中添加代码

```shell
#!/bin/bash

# 记录开始时间
START_TIME=$(date +'%Y-%m-%d %H:%M:%S')

# 定义发送钉钉通知的函数
send_dingtalk_msg() {
  local status=$1  # 成功/失败
  local color=$2   # ✅/❌
  local content="【Jenkins部署通知】
📌 任务名称：$JOB_NAME
🔢 构建编号：$BUILD_NUMBER
📅 开始时间：$START_TIME
🕒 结束时间：$(date +'%Y-%m-%d %H:%M:%S')
🔍 构建状态：$color $status
🌐 构建地址：$BUILD_URL"

  # 发送通知
  curl -s -X POST "机器人的Webhook" \
  -H "Content-Type: application/json" \
  -d "{
    \"msgtype\": \"text\",
    \"text\": {
      \"content\": \"$content\"
    }
  }"
}

# ...原部署项目代码

# 部署成功，发送通知
send_dingtalk_msg "部署成功" "✅"

exit 0

# 异常捕获
send_dingtalk_msg "部署失败" "❌"
exit 1
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260317234728009.png)

## 测试

1. 在 Jenkins 中执行构建任务。如果使用插件方式，一定要在任务中配置钉钉机器人，否则不会收到通知。
2. 查看钉钉群聊，是否收到部署成功/失败的通知。

## 总结

插件方式配置钉钉机器人比较方便，缺点是只能使用 Jenkins 内置的环境变量。
执行 shell 脚本中添加代码的方式更加灵活，可以自定义消息内容。
