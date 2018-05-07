# Laravel 5.6 为命令行错误报告添加了 collision 包

由 [nunomaduro](https://github.com/nunomaduro/) 创建和维护的 `Collision` 是PHP命令行应用程序的错误处理程序包。从Laravel 5.6开始，应用程序将包含 Collision 软件包的 `dev` composer 依赖。

当通过命令行或CLI与Laravel应用程序进行交互时，该软件包可为你提供美观的错误报告。

你会在你的终端中得到如下错误报告：

![collision-image](https://phpcasts.org/uploads/posts/collision-image.png)

Collision 建立 [whoops](https://github.com/filp/whoops)之上，whoops是在Laravel 5.5引入的。它支持Laravel artisan和PHPUnit。 前往 [Collision Github](https://github.com/nunomaduro/collision) 查看关于此软件包的更多信息。

> 原文地址： https://tutsforweb.com/laravel-5-6-collision/