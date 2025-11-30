🌟 一、核心功能

定时发送邮件

通过 GitHub Actions 或定时任务（如 cron）在固定时间自动运行。

可每日、每周或自定义时间间隔自动执行。

群发邮件

一次性给多个收件人发送邮件（如通知、报告、提醒、营销邮件等）。

邮件内容可以是固定模板，也可以动态生成（例如日报、统计结果、天气信息等）。

自动内容更新

邮件内容通常会自动从 API、数据库、RSS、或文件生成，比如：

每日天气报告

新闻摘要

GitHub 仓库状态更新

网站数据监控结果等。
# 注意
worker方式部署的，如果使用了最新脚本无法访问前端网页，需要先进到绑定的kv空间，清空kv里保存的内容！

## 部署方式一：github action（推荐）

在你的 GitHub 仓库中，依次点击 Settings -> Secrets -> Actions，然后点击 New repository secret，创建一个名为`EMAIL_CONFIG`的机密变量，内容为你的邮件配置信息。

`EMAIL_CONFIG`机密变量的`JSON`格式如下：
```
{
  "smtp_server": "smtp.example.com",
  "smtp_port": 587,
  "smtp_user": "your_email@example.com",
  "smtp_pass": "your_password",
  "from_email": "your_email@example.com",
  "to_emails": [
    "recipient1@example.com",
    "recipient2@example.com",
    "recipient3@example.com",
    "recipient4@example.com",
    "recipient5@example.com"
  ],
  "subject": "定时邮件通知",
  "body": "这是一封来自自动化脚本的邮件。"
}
```
**若需要tg通知，则新增以下两个变量**

- TG_ID = tg机器人用户ID

- TG_TOKEN = tg机器人token

**手动运行一次action，之后便可自动运行，默认为每周一次。可自行在action的yml配置文件中修改自动执行频率**

## 部署方式二：cf worker

到 [resend](https://resend.com/) 注册一个账号，申请 `apitoken`，并且绑定一个域名，根据 resend 的提示到域名托管商处添加相应的 dns 解析记录，有三个 `txt` 和一个 `mx` 记录。

在 cf 新建一个 wokrer，粘贴仓库内 `resend.js` 中的内容

已完成前端界面

![](https://pan.811520.xyz/2025-01/1736779999-%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20250113224844.webp)

### 设置以下环境变量：

- RESEND_API_KEY = 填刚刚申请的 `apitoken`
- KEY = 设置一个密码，访问`https://你项目的worker域名?key=你设置的密码`，即为前段面板
- FROM_EMAIL = 发件人邮箱，邮箱域名必须与在 resend 中绑定的域名一致，前缀随意
- TO_EMAILS = 收件人邮箱，支持多个邮箱地址，每行一个
- TG_ID = TG 机器人的 chat id
- TG_TOKEN = TG 机器人的 token
- SUBJECT = 邮件主题
- BODY = 邮件正文

### 绑定KV
- KV绑定变量名/KV空间名：AUTO_EMAIL

### 设置 `corn` 触发器
实现定时自动群发邮件，建议每周执行一次
