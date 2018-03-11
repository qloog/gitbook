# 控制器

## 控制器的定义

### 控制器文件

    - 文件位置:  位于application/controllers 或 application/modules/controllers 目录下
    - 文件命名:  首字母大写，不含Controller, 例如：User.php

### 控制器类

    - 类定义: 控制器明文件名+Controller, eg.: UserController
    - 类继承: 一般需要继承基类Yaf\Controller_Abstract或Yaf\Controller_Abstract的子类

### 控制器方法

    - 方法定义: 方法名+Action (对外可以访问)
    - 初始化方法: 代替自带的__construct, 在yaf中init都会被默认执行
    
### 命名空间

    Yaf框架的控制器默认情况下不需要指定namespace, 即使在多模块的情况下也不需要区分。这一点需要注意下。    
    
    
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

    - 作用: 为了分解大的控制器， 让代码更清晰
    - 与控制器绑定，即将操作绑定到某个类上，如
    
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
        
    - 定义action类文件
        - 位置: 一般在application/actions下面，如detail.php(application/actions/detail.php)
        - 命名: 必须以Action为后缀，如DetailAction
        - 继承: 继承自Yaf\Action_abstract
        - 必须实现的方法: execute,因为其父类Yaf\Action_abstract是一个抽象类，必须实现抽象方法execute
        - 可以使用父类的方法: 不仅可以使用Yaf\Action_abstract的方法，还可以使用Yaf\Controller_Abstract的方法(因为Yaf\Action_abstract继承自Yaf\Controller_Abstract)

## 控制器类方法

### 初始化操作

     - init: 初始化操作(如初始化实例变量，替代__construct的功能)
     - initView: 初始化试图
     
### 试图相关
     
### 请求与响应

     - getRequest: 获取当前请求对象，那么它就可以调用request对象所有的方法
     - getResponse: 获取当前响应对象，那么它就可以调用response对象所有的方法
     
### 页面跳转

     - fowrard: 
         - 跳转到某个控制器的某个方法，不会立即跳转，会继续执行他后面的内容
         - 支持跳转到其他模块的某个控制器的某个方法
     - redirect: 跳转到一个指定的url

          
 例如：登录权限控制
 
 - forward 跳转到当前控制器的
 ```
 <?php
    class IndexController extends \Yaf\Controller_Abstract
    {
        public function indexAction()
        {   
             $logined = $_SESSION["userId"];
             if (!$logined) {
                 $this->forward("login", array("from" => "Index")); // 跳转到当前模块的login操作
                 
                 return FALSE;  //立即返回就不会执行后面的内容了
             }
     
             //显示内容
        }
     
        public function loginAction() {
             echo "login, redirected from ", $this->_request->getParam("from") , " action";
        }
    }
 ?>
 ```
 
 - forward 跳转到其他模块
 
 ```
 //跳转到admin模块的login控制器的index方法
 $this->forward('admin', 'login', 'index',array('from'=>'index'));  
 ```

 - redirect 到某个url
 ```
 public function indexAction()
 {
    $logined = $_SESSION['user_id'];
    if (!$logined) {
        $this->redirect('http://www.domain.com/admin/login/index')); // 跳转到http://www.domain.com/admin/login/index这个地址
        
        return FALSE;  //立即返回就不会执行后面的内容了
    }
     
 }
 ```
 
## 错误处理控制器

Yaf中可以通过ErrorController(application/controllers/Error.php)控制器来捕获异常与错误。

捕获异常与错误需要开启相应的配置，有两种方式:

 - 通过配置文件开启
 
 ```
 // conf/application.ini
 application.dispatcher.catchException = true
 ```
 
 - 在框架启动文件中开启
 
 ```
 // application/Bootstrap.php
 public function _initException(Yaf\Dispatcher $dispatcher)
 {
     $dispatcher::getInstance()->catchException(true);
 }
 
 ```
 
 - 示例
 
 ```
    // application/controllers/Error.php
    <?php
    class ErrorController extends Yaf\Controller_Abstract
    {
        /**
         * @param Exception $exception
         */
         public function errorAction(Exception $exception)
         {
            $constArr = array(
                YAF\ERR\NOTFOUND\MODULE,
                YAF\ERR\NOTFOUND\CONTROLLER,
                YAF\ERR\DISPATCH_FAILED,
                YAF\ERR\NOTFOUND\ACTION,
                YAF\ERR\NOTFOUND\VIEW
            );
            $err = $exception->getCode();
            if (in_array($err, $constArr)) {
                $code = 404;
                $message = '请求的页面不存在';
            } else {
                $code = 500;
                $message = '系统出错';
            }
            if (ENV == 'DEV') {
                $message = $exception->getMessage();
            }
            //记录日志
            //ajax输出或显示错误模板
            $this->getView()->assign('message', $message);
        }
    }
 ```
 
 > 关于异常处理后面会有详细的介绍