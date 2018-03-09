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

    * 作用: 为了分解大的控制器， 让代码更清晰
    * 与控制器绑定，即将操作绑定到某个类上，如
        ```
        <?php
         class TestController extends \Yaf\Controller_Abstract
         {
             public $actions = array(
                 'index'=>'actions/index.php',
                 'add'=>'actions/add.php',
                 'detail'=>'actions/detail.php'  
             );
         }
        ```
    * 定义action类文件
        - 位置: 一般在application/actions下面，如detail.php(application/actions/detail.php)
        - 命名: 必须以Action为后缀，如DetailAction
        - 继承: 继承自Yaf\Action_abstract
        - 必须实现的方法: execute,因为其父类Yaf\Action_abstract是一个抽象类，必须实现抽象方法execute
        - 可以使用父类的方法: 不仅可以使用Yaf\Action_abstract的方法，还可以使用Yaf\Controller_Abstract的方法(因为Yaf\Action_abstract继承自Yaf\Controller_Abstract)

## 控制器类

## 错误控制器