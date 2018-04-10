# 编写API

Laravel 开发人员最常见的任务之一就是创建一个API，通常是JSON和REST或类似REST，允许第三方与Laravel应用程序的数据进行交互。
Laravel使得使用JSON非常容易，其资源控制器已经围绕REST动词和模式构建。 在本章中，我们将学习一些基本的API编写概念，Laravel提供的用于编写API的工具以及编写第一个Laravel API时需要考虑的一些外部工具和组织系统。

## 类似REST的JSON API的基础知识

具象状态传输（REST）是构建API的架构风格。 从技术上讲，REST是一个广泛的定义，可以适用于几乎整个互联网，所以不要让自己被定义所淹没，或陷入与嘲笑的争论中。 当我们在Laravel世界中讨论REST风格或类似REST的API时，我们通常会讨论具有几个共同特征的API：

- 围绕可由URI唯一表示的 "资源" 进行结构化，例如 `/cats` 用于表示所有的猫，对于ID为15的单个猫，用 `/cats/15` 等等。
- 与资源的交互主要通过使用HTTP动词（ GET `/cats/15` 与 DELETE `/cats/15`）
- 无状态，这意味着请求之间没有持续的会话认证; 每个请求必须唯一地验证自己
- 可缓存且一致，意味着每个请求（除少数经过身份验证的用户特定请求）都应返回相同的结果，而不管请求者是谁
- 返回JSON

最常见的API模式是针对每个暴露为API资源的Eloquent模型都有一个唯一的URL结构，并允许用户使用特定动词与该资源进行交互并返回JSON。 下面给出了一些可能的例子。

```json
GET /api/cats
[
    {
        id: 1,
        name: 'Fluffy'
    },
    {
        id: 2,
        name: 'Killer'
    }
]

GET /api/cats/2
{
    id: 2,
    name: 'Killer'
}

DELETE /api/cats/2
deletes cat

POST /api/cats with body:
{
    name: 'Mr Bigglesworth'
}
(creates new cat)

PATCH /api/cats/3 with body:
{
    name: 'Mr. Bigglesworth'
}
(updates cat)
```

可以看到我们可能与我们的API进行的基本交互。 让我们来挖掘到如何让它们与Laravel一起发生。

## 控制器组合和JSON返回

## 去取和发送header

## Eloquent 分页

## 排序和过滤

## 转换结果

## 筑巢和关系

## 使用 Laravel Passport 认证

### 关于OAthon 2.0 的简短介绍

### 安装Passport

### Passport 的API

### Passport 有效的授权类型

### 用Passport API 和 Vue 组件管理客户端和token

### Passport 范围

## Laravel 5.2+ API token认证

## 单元测试

## 总结