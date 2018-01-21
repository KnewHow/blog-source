# Sprint 4 Play与异步编程

## 目标

熟悉 Play 框架和全异步编程的套路

## Task1

> 时长 2d ~ 3d

+ 用创建一个 `Play` 的工程
+ 引入我们内部使用的 `quill` 0.7.25 版本作为数据库访问框架，可以参考其他工程
+ 实现用户注册功能
  * 注册时需填写邮箱，用户名密码，头像，城市
  * 用户名必须是6-12英文和数字组合
  * 密码必须是6-12英文和数字组合
+ 实现用户登入功能
  * 用户不存在|用户密码不匹配能给出相应提示
  * 登入后调转到用户首页显示用户名和当前用户和天气情况

### 要求

+ 使用[天气接口](http://samples.openweathermap.org/data/2.5/weather?q=HangZhou&appid=b1b15e88fa797225412429c1c50c122a1)获取天气，可直接显示英文
+ 可以使用 [IBM ICU4J](http://site.icu-project.org/) 实现中文到拼音转换
+ 实现是全异步的
+ 用户表包含 `email`, `username`, `avatar`, `city` , `password`, `gmt_create`, `gmt_modified` 字段
+ 实现时考虑 SQL 注入，XSS 注入等安全问题
+ 头像保存本地路径可以通过配置文件指定


## Task 2

> 时长 1d ~ 2d

实现基于 WebSocket 的聊天功能

+ 登入后显示最近20条聊天记录
+ 一旦有用户发送了消息则在所有在线的用户显示该消息
