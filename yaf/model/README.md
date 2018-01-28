# 模型

## 概述

> Yaf框架中本身是没有提供数据库操作相关的类，但是我们可以集成第三方的ORM(比如Eloquent, Doctrine), 或者对PDO简单封装的类库。

## 模型定义
    
 - 文件位置： 位于`application/models`目录下
 - 命名规则： 首字母大写的形式，不含 Model，如`User.php`
 - 类定义： 文件名+Model 作为类名，如 `class UserModel extend Model {}`  
 - 继承： 继承自Model基类（ORM或者基于PDO封装好的基类）  
 
 示例:
 
 ```php
 <?php
 
 use PHPCasts\Model
 
 class UserModel extends Model
 {
 
 }

 ```

## ORM

### 基本功能

 - 连接数据库
 - CURD功能
 
### 实现

可以选用自己熟悉的一个第三方的ORM，或者封装好的类库，常见的有如下几种：

 - Eloquent（推荐）
 - 自己封装的类库 Model

 
> 其中 `Eloquent`, `Doctrine` 内部封装的比较抽象，功能也挺强大，但是性能差一点，适用于OA、CMS等系统使用
> `Medoo` 和 `自己封装的类库` 因为是对PDO的简单封装，所以性能相对对好很多。 适用于Web或者API等性能要求高的场景。
 
 以`自己封装的类库` 为例：
 
 - 连接数据库
 - CURD操作
 - 调试

## 使用

### Eloquent

Laravel的自带的ORM，可以很方便的与其他框架配合使用。

#### 与框架集成

```
public function _initDbAdapter(Dispatcher $dispatcher)
    {
        $capsule = new Capsule();
        $db = $this->config['database'];
        $capsule->addConnection($db);
        $capsule->setEventDispatcher(new LDispatcher(new LContainer));
        $capsule->setAsGlobal();
        $capsule->bootEloquent();

        // 为类创建别名，便于在脚本或者其他地方直接通过DB::的形式来直接使用
        class_alias('\Illuminate\Database\Capsule\Manager', 'DB');
    }
```

#### 示例

```
<?php

use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Model;

class UserModel extends Model
{

}
```

具体使用可以参考Laravel的官方使用示例


> 以自定义的PDO封装的库为例

### 自定义基础PDO的类库（Model）

#### 配置

```

database.type = mysql                           ;声明数据库类型
database.hosts = commondb                       ;声明数据库主机的别名，如果多台请使用,隔开
database.default.server = commondb              ;默认的主机别名
     
database.commondb.read.host = 127.0.0.1         ;commondb这个库的读库主机IP
database.commondb.read.port = 3306
database.commondb.read.dbname = commondb
database.commondb.read.user = shixinke
database.commondb.read.password = 123456
database.commondb.read.prefix = tbl_            ;commondb这个库的表前缀
     
database.commondb.write.host = 127.0.0.1        ;commondb这个库的写库主机IP
database.commondb.write.port = 3306
database.commondb.write.dbname = commondb
database.commondb.write.user = shixinke
database.commondb.write.password = 123456
database.commondb.write.prefix = tbl_

```

#### 初始化数据库连接

主要代码如下:

```
private function getDbConfig()
{
     return \Yaf\Registry::get('config');
}

private function getConnect()
{
    $config = $this->getDbConfig();
            $type = strtolower($type);
            if (!$this->server) {
                $this->server = $config['database']['default']['server'];
            }
            $db     = $this->database ? $this->database : $config['database'][$this->server][$type]['dbname'];
            $driver = $config['database']['type'];
            $host   = $config['database'][$this->server][$type]['host'];
            $port   = $config['database'][$this->server][$type]['port'];
            $user   = $config['database'][$this->server][$type]['user'];
            $pwd    = $config['database'][$this->server][$type]['password'];
            $persistent = isset($config['database'][$this->server][$type]['pconnect']) ? $config['database'][$this->server][$type]['pconnect'] : 0;
            if($persistent){
                $option = array(PDO::ATTR_PERSISTENT => 1);
            }else{
                $option = array();
            }
     
            if(!$port){
                $port = 3306;
            }
     
            $dsn = $driver.':host='.$host.';port='.$port.';dbname='.$db;
     
            try{
                // 判断 READ, WRITE 是否是相同的配置, 是则用同一个链接, 不再创建连接
                $read_host = $config['database'][$this->server]['read']['host'];
                $read_port = $config['database'][$this->server]['read']['host'];
     
                $write_host = $config['database'][$this->server]['write']['host'];
                $write_port = $config['database'][$this->server]['write']['host'];
     
                if($read_host == $write_host && $read_port == $write_port){
                    $sington = TRUE;
                }
     
                if($sington){
                    if(isset(self::$obj)) {
                        if(isset(self::$obj[$this->server]['read'])) {
                            self::$obj[$this->server]['write'] = self::$obj[$this->server]['read'];
                        }else{
                            self::$obj[$this->server]['read'] = self::$obj[$this->server]['write'];
                        }
     
                        self::$conn = self::$obj[$this->server]['write'];
                    }
                }
     
                // 读写要分离则创建两个连接
                if(!isset(self::$obj[$this->server][$type])) {
                    self::$conn = self::$obj[$this->server][$type] = new PDO($dsn, $user, $pwd, $option);
                    self::$conn->query('SET NAMES `utf8`');
                    unset($db, $driver, $host, $port, $user, $pwd, $dsn);
                }
            }catch(PDOException $e){
                if(ENV == 'DEV'){
                    throw new Exception($e->getMessage());
                }else{
                    //写入日志
                }
            }
    
    }
```

#### 与框架集成

直接放到框架可以加载到的地方即可。比如`Library/Model/Model.php`, 然后继承该文件。


#### 示例

```
<?php
 
 use PHPCasts\Model
 
 class UserModel extends Model
 {
     
 }


```




