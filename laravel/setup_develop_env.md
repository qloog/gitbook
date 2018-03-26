# 安装Laravel开发环境

PHP成功的部分就是因为很难去找到一个不能运行PHP的web server。然而现代的PHP工具都比过去的那些有更严格的要求。
用Laravel开发最好的方式是确保有一致的本地和远程服务环境去运行代码，很高兴Laravel生态提供了这些工具。

## 系统要求

本文所覆盖的所有内容在适用于运行在Windows机器上，但是可能会有很多的操作和说明。如果你是Windows用户，你也可以根据本文内容去实际操作。
下面所说的内容主要是针对Unix/Linux/macOS上的开发者。

不管你是在本地环境上安装虚拟机`Vagrant`来运行你的开发环境，然后安装PHP和其他工具运行你的站点，还是安装MAMP/WAMP/XAMPP这些工具，你的开发环境都需要去安装下面的依赖来启动你的Laravl站点或应用：

 - PHP>=7.1.3(Laravel5.6) 和 PHP>=7.0.0(Laravel5.5) 和 PHP>=5.6.4(Laravel5.3和5.4) 或 PHP>=5.5.9(5.1和5.2)
 - OpenSSL PHP扩展
 - PDO PHP扩展
 - Mbstring PHP扩展
 - Tokenizer PHP扩展
 - XML PHP扩展 (Laravel>=5.3)
 - Ctype PHP扩展 (Laravel>=5.6)
 - JSON PHP扩展 (Laravel>=5.6)

### Composer

不管你在什么机器上做开发都需要全局安装`Composer`。如果你不熟悉`Composer`也没关系，它其实是一个现代化PHP开发的一个基础工具。 
`Composer`用于PHP的依赖管理，类似Node的NPM，Ruby的RubyGems。你需要Composer安装Laravel, 更新Laravel, 引入额外的依赖等等。

### 本地开发环境

对于大多数项目而言，使用一个简单的工具包来搭建开发环境已经足够。如果你的系统上已经安装了`MAMP`或`WAMP`或`XAMPP`,也可以很好的运行Laravel。
通过使用PHP的内置Server也可以运行Laravel,当然前提是PHP版本是满足要求的。

你真正需要开始的是你运行PHP的能力，这一些都取决于你。

然而，Laravel也提供了两个本地开发环境工具: `Valet`和`Homestead`, 后面部分都会提到。如果你不确定使用哪一个，我推荐你使用`Valet`, `Homestead`简单略看下就可以了。不管怎样，两个工具都是非常有价值的，也值得理解。

#### Laravel Valet

如果你想使用PHP内建的web server， 通过使用最简单的参数选项就可以运行一个站点。在Laravel站点的根目录下运行
`php -S localhost:8000 -t public`, 就可以通过内建web server来访问你的站点，如： `http://localhost:8000`。
也可以通过Laravel提供的命令来运行你的应用`php artisan serve`。

但是如果你对每一个站点从绑定到一个指定的开发域名感兴趣的话，你需要熟悉操作系统的host文件，可以使用第三方工具来操作host，
比如[dnsmasq](http://bit.ly/2eNPJ5T)。让我们来看下更简单的方式。

如果你是Mac用户，Laravel Valet不需要将域名指向到应用的目录。Valet安装了 dnsmasq和一系列的PHP脚本，可以让我们输入`laravel new myapp && open myapp.dev`之后正常工作。你需要使用Homebrew安装一些工具，文档将会引导你完成这些工作，但从初始安装到为你的应用提供服务的步骤非常简单。

安装Valet(看[文档](https://laravel.com/docs/5.6/valet)的最新安装说明，将其指向一个或多个网站将要存放的目录。
我在`~/Sites`目录中运行 `valet park`，然后添加`.dev`到目录名称的结尾，就可以在浏览其中进行访问了。

Valet可以非常容易的服务给定目录里的所有目录作为`FOLDERNAME.dev`，通过使用`valet park`, 使用`valet link`服务于单个目录， 使用`valet open`打开valet服务的域名，使用`valet secure`服务于HTTP的站点，也可以使用`valet share`打开一个`ngrok`通道的站点去分享给其他人。

#### Laravel Homestead

Homestead 可能是你想去使用安装本地开发环境的另一个工具。它是一个基于Vagrant的 配置 工具，提供了个预配置的虚拟机镜像,专为Laravel开发而设定。许多生产环境的Laravel站点也都运行在上面。

##### 建立Homestead

如果你选择使用Homestead, 可能要比安装像MAMP、Valet多做一点工作。当然益处也是很大的，通过正确的配置，你的本地环境会非常接近于远程工作环境，也就不必担心更新本地机器上的依赖，你也可以完全了解Ubuntu服务器的结构从本地机器上。

Homestead都提供了什么工具？它提供了一些非常重要的工具，这些都是默认自带的：

 - 要运行的服务器和移动站点的工具：Ubuntu, PHP(5.6, 7.0, 7.1, 7.2) 和 Nginx, Apacche
 - 数据库存储和队列，如：Mysql,PostgreSQL,Redis,Memcached
 - 构建任务和资源管理的一些工具，都是基于Node的，如：Yarn, Bower, Grunt, Gulp
 - 其他的一些常用工具：Git, Composer


##### 安装Homestead依赖 

首先，需要下载安装`VirtualBox`或者`VMWare`。`VirtualBox`是最常用的，因为它是免费的。
其次，下载和安装`Vagrant`.
`Vgrant`是非常方便的，可以让你非常容易的创建一个本地虚拟机从一个预创建的`box`，这个box本质上是一个虚拟机的模板。
所以下一步就是在终端运行命令来下载box: `vagrant box add laravel/homestead`

##### 安装Homestead

来实际安装下Homestead. 你可以安装多个Homestead实例(每一个项目使用一个不同的Homestead),但我更喜欢单个的Homestead虚拟机为所有的项目。
如果每个项目一个的话，你需要在每个项目的目录里安装Homestead, 可以看 [Homestead在线文档](https://laravel.com/docs/5.6/homestead)。
如果为所有项目使用一个虚拟机的话，可以使用以下命令在当前用户的home目录下安装Homestead：

```
git clone https://github.com/laravel/homestead.git ~/Homestead
```

运行homestead的初始化脚本

```
bash ~/Homestead/init.sh
```

脚本会新建一个目录`~/.homestead`，并将主要的配置文件`Homestead.yaml`放到该目录里。

##### 配置Homestead

打开配置文件 `Homestead.yaml`，对其按照自己的计划进行配置。下面是一个示例：

```
    ip: "192.168.10.10"
    memory: 2048
    cpus: 1
    provider: virtualbox

    authorize: ~/.ssh/id_rsa.pub

    keys:
        - ~/.ssh/id_rsa

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          
    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public

    databases:
        - homestead

    # blackfire:
    #     - id: foo

    # token: bar
    #       client-id: foo
    #       client-token: bar

    # ports:
    #     - send: 50000
    #       to: 5000
    #     - send: 7777
    #       to: 777
    #       protocol: udp
```

##### 在Homestead中创建数据库

##### 准备Homestead

##### 日常使用Homestead

##### 从桌面客户端连接到Homestead中的数据库


## 创建一个新的Laravel项目

### 用Laravel 安装器安装Laravel

### 使用Composer的 create-project 安装Laravel

## Laravel的目录结构

### 目录

### 一些其他文件

## 配置

## 启动和运行

## 测试

## TL;LDR