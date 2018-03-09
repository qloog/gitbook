# 控制器

## 控制器的定义

### 控制器文件

    * 文件位置:  位于application/controllers 或 application/modules/controllers 目录下
    * 文件命名:  首字母大写，不含Controller, 例如：User.php

### 控制器类

    * 类定义: 控制器明文件名+Controller, eg.: UserController
    * 类继承: 一般需要继承基类Yaf\Controller_Abstract或Yaf\Controller_Abstract的子类

### 控制器方法

    * 方法定义: 方法名+Action (对外可以访问)
    * 初始化方法: 代替自带的__construct, 在yaf中init都会被默认执行
    
### 示例

```
// application/controller/User.php
<?php

class UserController extends \Yaf\Controller_Abstract
{
    // 该方法默认会被执行
    public function init()
    {
        
    }
    
    public function messageAction()
    {
    
    }
}
?>
```
    

## 独立文件Action的定义

    * 作用


## 控制器类

## 错误控制器