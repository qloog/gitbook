# 模型

## 概述

> Yaf框架中本身是没有提供数据库操作相关的类，但是我们可以集成第三方的ORM(比如Eloquent, Doctrine), 或者对PDO简单封装的类库。

## 模型定义
    
 - 文件位置： 位于`application/models`目录下
 - 命名规则： 首字母大写的形式，不含 Model，如`User.php`
 - 类定义： 文件名+Model 作为类名，如 `class UserModel extend Model {}`  
 - 继承： 继承自Model基类（ORM或者基于PDO分装好的基类）  
 
 示例
 ```php
 <?php
 
 use PHPCasts\Model
 
 class PostsModel extends Model
 {
 
 }

 ```

## ORM

### 基本功能

 - 连接数据库
 - CURD功能
 
### 实现

可以选用自己熟悉的一个第三方的ORM，或者封装好的类库，常见的有如下几种：

 - Medoo
 - Eloquent
 - Doctrine
 - 自己封装的类库
 
> 其中 `Eloquent`, `Doctrine` 内部封装的比较抽象，功能也挺强大，但是性能差一点，适用于OA、CMS等系统使用
> `Medoo` 和 `自己封装的类库` 因为是对PDO的简单封装，所以性能相对对好很多。 适用于Web或者API等性能要求高的场景。
 
 以`自己封装的类库` 为例：
 
 - 连接数据库
 - CURD操作
 - 调试

## 使用

实现一个Demo