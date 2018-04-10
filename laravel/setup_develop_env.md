# 安装Laravel本地开发环境

PHP成功的一部分因为就是很难去找到一个不能运行PHP的Web Server。然而现代的PHP工具都比过去的那些有更严格的要求。
用Laravel开发最好的方式是确保有一致的本地和远程服务环境去运行代码，很高兴Laravel生态提供了这些工具。

### 系统要求

本文所覆盖的所有内容在适用于运行在Windows机器上，但是可能会有很多的操作和说明。如果你是Windows用户，你也可以根据本文内容去实际操作。
下面所说的内容主要是针对Unix/Linux/macOS上的开发者。

不管你是在本地机器上安装虚拟机`Vagrant`来运行你的开发环境，然后安装PHP和其他工具运行你的网站，还是安装MAMP/WAMP/XAMPP这些工具，你的开发环境都需要去安装下面的依赖来启动你的Laravel网站或应用：

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
通过使用PHP的内置Server也可以运行Laravel,当然前提是PHP版本是满足框架要求的。

很高兴Laravel也提供了两个本地开发环境工具: `Valet`和`Homestead`, 后面部分都会提到。如果你不确定使用哪一个，我推荐你使用`Homestead`, `Valet`简单略看下就可以了。不管怎样，两个工具都是非常有价值的，也值得理解。

#### Laravel Valet

如果你想使用PHP内建的Web Server， 通过使用最简单的参数选项就可以运行一个站点。在Laravel站点的根目录下运行
`php -S localhost:8000 -t public`, 就可以通过内建Web Server来访问你的站点，如： `http://localhost:8000`。
也可以通过Laravel提供的命令来运行你的应用`php artisan serve`。

但是如果你对每一个站点从绑定到一个指定的开发域名感兴趣的话，你需要熟悉操作系统的host文件，可以使用第三方工具来操作host，
比如[dnsmasq](http://bit.ly/2eNPJ5T)。让我们来看下更简单的方式。

如果你是Mac用户，Laravel Valet不需要将域名指向到应用的目录。Valet安装了 dnsmasq和一系列的PHP脚本，可以让我们输入`laravel new myapp && open myapp.dev`之后正常工作。你需要使用Homebrew安装一些工具，文档将会引导你完成这些工作，但从初始安装到为你的应用提供服务的步骤非常简单。

安装Valet(看[文档](https://laravel.com/docs/valet)的最新安装说明，将其指向一个或多个网站将要存放的目录。
我在`~/Sites`目录中运行 `valet park`，然后添加`.dev`到目录名称的结尾，就可以在浏览其中进行访问了。

Valet可以非常容易的服务给定目录里的所有目录作为`FOLDERNAME.dev`，通过使用`valet park`, 使用`valet link`服务于单个目录， 使用`valet open`打开valet服务的域名，使用`valet secure`服务于HTTP的站点，也可以使用`valet share`打开一个`ngrok`通道的站点去分享给其他人。

#### Laravel Homestead

Homestead 可能是你想去使用安装本地开发环境的另一个工具。它是一个基于Vagrant的配置工具，提供了个预配置的虚拟机镜像，专为Laravel开发而设定。许多生产环境的Laravel站点也都运行在上面。

##### 建立Homestead

如果你选择使用Homestead, 可能要比安装像MAMP、Valet多做一点工作。当然益处也是很大的，通过正确的配置，你的本地环境会非常接近于远程工作环境，也就不必担心更新本地机器上的依赖，你也可以完全从本地机器上了解Ubuntu服务器的结构。

Homestead都提供了什么工具？它提供了一些非常重要的工具，这些都是默认自带的：

 - 要运行的服务器和运行站点的工具：Ubuntu, PHP(5.6, 7.0, 7.1, 7.2) 和 Nginx, Apache
 - 数据库存储和队列，如：MySQL, PostgreSQL, Redis, Memcached
 - 构建任务和资源管理的一些工具，都是基于Node的，如：Yarn, Bower, Grunt, Gulp, Webpack
 - 其他的一些常用工具：Git, Composer


##### 安装Homestead依赖 

首先，需要下载安装 [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 或者 `VMWare`。`VirtualBox`比较常用而且也是免费的。

其次，下载和安装 [Vagrant](https://www.vagrantup.com/downloads.html)。`Vagrant`是非常方便的，可以让你非常容易的从一个预创建的`box`创建一个本地虚拟机，这个box本质上是一个虚拟机的模板。所以下一步就是下载并安装box。

下载前先来简单了解一下`vagrant box add`这条命令

```
➜  ~ vagrant box add -h
Usage: vagrant box add [options] <name, url, or path>
```

可以看到该命令支持 `name`，`url`和`path` 安装， 本质其实就是要找到一个box。

###### 在终端运行命令来下载(name方式)

```shell
vagrant box add laravel/homestead
```

###### 手动下载安装(path方式)

注意的是，这个box大约有1.5G，下载可能会很慢，所以可以考虑通过迅雷来下载(命令下载过程中会有显示box的url)，下载成功之后，运行：

```shell
vagrant box add laravel/homestead ~/Downloads/virtualbox.box
```

##### 安装Homestead

来实际安装下Homestead. 你可以安装多个Homestead实例(每一个项目使用一个不同的Homestead),但我更喜欢单个的Homestead虚拟机为所有的项目。如果每个项目一个的话，你需要在每个项目的目录里安装Homestead, 可以看 [Homestead在线文档](https://laravel.com/docs/homestead)。如果为所有项目使用一个虚拟机的话，可以使用以下命令在当前用户的home目录下安装Homestead：

```shell
git clone https://github.com/laravel/homestead.git ~/Homestead
```

运行homestead的初始化脚本

```shell
cd ~/Homestead

// Mac/Linux
bash init.sh
```

##### 配置Homestead

打开配置文件 `Homestead.yaml`，对其按照自己的计划进行配置。下面是一个可以直接使用的示例：

```yaml
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
        - map: homestead.test
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

你需要告诉Homestead.yaml配置文件用哪个`provider`，也就是指定需要使用的虚拟机, `authorize`是需要指向到ssh公钥(默认位于当前用户目录的.ssh目录下`~/.ssh/id_ras.pub`, 不知道如何生成的话，可以看Github提供的[生成ssh key的简明教程](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)), `folders`和`sites`类似都是映射到本地机器目录，只不过一个是映射目录，一个是域名映射，还需要准备一个数据库，基本的配置就这么多。

在Homestead中的映射目录是允许你在本地机器编辑，也就是把目录里的文件放到了Vagrant box里，这样就可以启动对应的站点或应用了。
例如如果你有一个放置你所有代码的目录`~/Sites`，你可以在Homestead配置文件中把该目录映射取代上面配置文件提到的部分，取代后：

```yaml
    folders:
        - map: ~/Sites
          to: /home/vagrant/Sites
```

这样就在Homestead虚拟机里创建了一个目录`/home/vagant/Sites`，并把它映射到了本地机器的`~/Sites`目录。

> 用于开发站点的顶级域名
> 对于本地开发站点，你可以使用任何习惯的域名，Homestead里建议使用`.test`，
> 所以如果我在为`myapp.com`站点做开发的话，我会在本地使用`myapp.test`域名进行开发。
> 严格上来说，`.app`和`.dev`都是有效的顶级域名， 所以如果使用它们用于自己内部使用，可能会和真实的域名发生冲突。
> 这里提供四个专门用做开发的顶级域名：`.example`,`.test`,`.invalid`, `.localhost`。

现在来建立我们的第一个示例站点，比如线上站点的域名是`projectName.com`，那我在Homestead.yaml里，我会映射本地开发目录到
`projectName.test`，所以会有一个单独的URL来访问本地开发环境下的站点。

```yaml
    sites:
        - map: projectName.app
          to: /home/vagrant/Sites/projectName/public
```

上面这段代码意思就是把`projectName.test`映射到了虚拟机里的 `/home/vagrant/Sites/projectName/public` 目录，该目录是Laravel项目下的`public`目录。

最后我们需要告诉本地机器，当你访问`projectName.test`的时候，会找本地机器的IP进行解析。
Mac和Linux的用户可以编辑`sudo vim /ets/hosts`, Windows用户编辑`C:\Windows\Sys‐ tem32\drivers\etc\hosts`, 添加下面这一行:

```bash
192.168.10.10   projectName.test
```

一旦Homestead准备好，你就可以通过浏览器访问`http://projectName.test`了。

##### 在Homestead中创建数据库

类似刚才你在Homestead.yaml定义site一样，也需要定义一个数据库。数据库的定义非常简单，因为你仅仅需要添加一个数据库即可，不用做其他任何操作。

```shell
    databases:
        - projectname
```

##### 准备Homestead

因为实际上是第一次启动Homestead box,所以需要告诉Vagrant去初始化。 进入到`Homestead`目录，然后执行`vagrant up`

```shell
    cd ~/Homestead
    vagrant up
```

现在你的Homestead box已经启动和运行，其实就是一个本地目录镜像，它将会提供给你一个可以在本地机器上的任何浏览器上访问的地址。
数据库也已经创建好。现在你已经有了一个运行环境，可以准备去建立第一个Laravel项目了。但是我们需要先快速的了解一下Homestead日常是如何使用的。

##### 日常使用Homestead

通常情况下，你会让Homestead虚拟机是一直处于运行状态，但是如果不是或者你需要重启你的电脑，那么你就需要知道怎样启动和关闭这个box.

既然Homestead是基于Vagrant命令的，那你也就可以使用Vagrant命令用于大部分Homestead的操作管理。
进入到安装Homestead的目录里，根据需要运行下面的命令：

```bash
vagrant up          启动homestead box
vagrant suspend     生成一个box快照，然后关闭box
vagrant halt        关闭整个box
vagrant destroy     删除box
vagrant provision   重启box
```

##### 从桌面客户端连接到Homestead中的数据库

如果你是使用像`Sequel Pro`这样的客户端，想要从本地机器连接到Homestead中的MySQL数据库，可以这样设置：

- Connection type: Standard (non-SSH)
- Host:127.0.0.1
- Username:homestead
- Password:secret
- Port:33060

## 创建一个新的Laravel项目

有两种方式可以创建一个新的Laravel项目，但两种都是运行在命令行下。第一种方式是使用Composer全局安装`Laravel installer`工具，第二种方式是使用Composer的 `create-project`选项。下面分别来说下。

### 通过Laravel 安装器安装Laravel

如果已经全局安装了Composer,那安装`Laravel installer`就简单多了。

```bash
    composer global require "laravel/installer"
```

一旦安装成功，新建一个Laravel项目也就变得非常简单了，运行如下命令

```bash
    laravel new projectName
```

### 通过Composer的 create-project 安装Laravel

Composer也提供了一个选项`create-project`用于创建一个包含骨架代码的新项目。要使用该工具来创建新Laravel项目的命令：

```bash
composer create-project laravel/laravel projectName --prefer-dist
```

和安装器类似，命令执行完以后会在当前目录创建一个`projectName`的子目录，该目录里包含Laravel的基本骨架，可以准备开发了。

## Laravel的目录结构

当开发新建的目录吼，你会发现包含了Laravel应用的基本骨架，包含的文件和目录如下

```bash
    app/ 
    bootstrap/ 
    config/ 
    database/ 
    public/ 
    resources/ 
    routes/ 
    storage/ 
    tests/ 
    vendor/
    .env 
    .env.example 
    .gitattributes 
    .gitignore 
    artisan 
    composer.json 
    composer.lock 
    gulpfile.js 
    package.json 
    phpunit.xml 
    readme.md 
    server.php
```

下面来一一熟悉一下

### 涉及到的目录

根目录下默认包含的文件夹有

- app          大多数实际应用的代码都会放到这里，比如 模型，控制器，路由定义，命令工具，以及所有php相关的代码
- bootstrap    包含了Laravel框架每次运行时都会启动的文件
- config       所有的配置文件都会放到这里
- database     存放数据库迁移和种子文件
- public       当启动一个站点是，server都会指向到该目录。该目录包含`index.php`, 是一个启动引导并正确路由所有请求的前端控制器，也是面向公众的文件，比如images,样式，脚本，或者下载文件等。
- resources    存放的一些非PHP文件，比如：模板文件，语言文件，Sass/LESS 和js文件的原文件。
- routes       所有的路由定义都放在这里，既包含HTTP路由，也包含console路由或者Artisan命令
- storage      主要存放缓存文件，日志文件，还有编译过的一些系统文件。
- tests        单元测试和继承测试文件放这里
- vendor       Composer安装的依赖放这里，但是这些依赖不在Git的版本控制里，我们期望Composer也是在远程server部署应用的一部分。

### 零散的文件

- .env和.env.example   指定环境变量的相关文件，我们期望每个环境使用不同的变量，所以该文件不能放到版本控制里。`.evn.example`是一个模板文件，每个环境都应该一样，有各自的`.env`文件，并且是git忽略的，也就是放到`.gitignore`里。
- artisan  该文件允许我们可以在命令行里运行artisan命令(后面会有讲到)
- .gitignore和.gitattributes  git的相关配置文件
- composer.json 和 composer.lock   用于Composer的配置文件，其中`composer.json`是可以编辑的，`composer.lock`是不能编辑的。`composer.json`可以显示出关于该项目的基本信息，也可以定义PHP相关的一些依赖。
- gulpfile.js  Elixir和Gulp的配置文件，用于编译和处理前端资源
- package.json 有点类似与composer.json，只不过是用于前端资源的。
- phunit.xml   单元测试的配置文件
- readme.md    一个Markdown文件，主要是对Laravel提供一个基本的介绍。
- server.php   一个备份服务器，试图让性能较差的服务器仍然可以预览Laravel应用。

## 配置

Laravel应用里的核心配置，比如数据库连接，队列，邮件配置等等，都放在`config`目录里。每一个文件都会返回一个数组，数组里的每一个值都是可以通过一个key来访问的。
这个key是由文件名和数组里的key组成的，然后用`.`分割。所以，如果你创建了一个文件`config/services.php`，如

```
// config/services.php
return [
    'sparkpost' => [
        'secret' => 'abcdefg'
    ];
]
```

那么如果你想获取数组里`secret`key的值，可以通过使用`config('services.sparkpost.secret')`来获取。

对于每个环境应该是不同的配置变量，所以这些变量应该保存在`.env`文件里。来看下如何在不同环境使用不同的API key.

```
// config/services.php
return [
    'weibo' => [
        'api_key' => env(WEIBO_API_KEY)
    ]
]
```

`env()`此函数主要是从`.env`文件里获取同名key的value值。所以需要添加`WEIBO_API_KEY`key到`.env`和`.env.example`文件中

```
// .env
WEIBO_API_KEY=8523KFJSD83583435
```

默认情况下，`.env`文件中已经包含了很多框架需要的变量，比如要使用到的邮件驱动，基本的数据库配置。

## 启动和运行

现在可以启动运行安装好的Laravel了。运行 `vagrant up`, 根据host里绑定的域名就可以访问配置好的网站了。

## 总结

由于Laravel是一个PHP框架，所以在本地运行也是非常的简单。Laravel提供了两种管理本地开发环境的工具：一个是Valet可以为本地机器提供依赖的简单工具，另一个是Homestead的预配置好的Vagrant。Laravel的依赖也可以通过Composer进行安装。介绍了一系列反映规范和其他开源工具关联的一些目录和文件。