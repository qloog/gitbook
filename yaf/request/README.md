# 请求

## 概述

代表了一个实际的请求，一般不用自己去实例化，Yaf_Application(开启命名空间后为Yaf\Application)在run以后会自动根据当前请求实例它。

在控制器中，可以通过`$this->getRequest()`来获取当前请求的对象。

### 对应的类

 - Yaf\Request_Abstract
 - Yaf\Request\Http
 - Yaf\Request\Cli

### 获取请求信息

#### 获取服务器及环境信息

* getServer:获取服务器变量信息，类似于$_SERVER全局变量
* getEnv:获取环境变量信息，类似于$_ENV全局变量
* getLanguage:获取客户端的语言信息，如zh-CN


#### 获取请求参数

* getPost：获取POST请求数据
* getQuery : 获取查询字符串的信息
* getParam : 获取路由参数信息
* getParams :　获取所有路由参数信息
* getRequestUri ： 获取请求的uri
* getMethod : 获取请求的类型
* getBaseUri : 获取请求的基本uri
* getCookie :　获取cookie信息 （可用浏览器cookie插件添加cookie来测试）
* getFiles : 获取请求中的文件信息 (可用postman测试) - Yaf-request-http里有


#### 判断请求类型

* isCli:是否为命令行
* isGet:是否为get请求
* isPost：是否为Post请求
* isPut：是否为PUT请求
* isHead:是否为Head请求
* isOptions:是否为Options请求
* isXmlHttpRequest:是否为ajax请求


#### 与分发相关的方法

#### 设置分发的方法