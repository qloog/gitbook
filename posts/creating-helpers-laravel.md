# 在Laravel中创建自己的PHP帮助函数

Laravel 为我们提供了许多内置的帮助函数，你可以在应用程序中的任何位置调用它。它们使你可以方便地处理数组和对象，路径，字符串，URL和其他类型的工作流。

尽管在 Laravel 核心中定义了许多帮助函数，但你可以在 Laravel 中定义自己的帮助函数以避免重复相同的代码。它确保你的应用程序有更好的可维护性。

我们来看看如何创建自己的定制 Laravel 帮助函数

## Laravel 中的帮助函数

Laravel 中有许多内置的帮助程序可供你的应用程序使用。它们根据它们提供的功能类型进行分组。这是一个完整的内置 Laravel 帮助文件。

### 数组和对象

在这个组中，助手提供了处理数组和对象的能力。 该组包含用于添加两个数组的辅助函数，将多维数组折叠成单个数组，返回数组的第一个元素，检查数组中是否存在给定的项目或项目，并执行许多其他类型的操作。

### 路径

这组helper返回Laravel应用程序中不同目录的绝对路径，例如 app，config，public，resource，storage和你的应用程序的基本路径。

### 字符串

该组中的助手使用字符串操作。 你可以将字符串转换为骆驼大小写，找到该类的基本名称，运行 `htmlspecialchars`，将文本转换为kebab大小写，将文本转换为大小写，并执行许多其他类型的字符串操作。

### URL

助手的URLs组与生成URL一起工作。你可以为控制器操作生成URL，命名为route，以及指定路径的完全限定URL。

### 杂项

这类帮助程序包含处理页面状态，服务容器，认证，缓存等功能。

## 在Laravel中创建帮助文件

在本节中，我们将通过创建Laravel帮助程序文件，该文件可以在Laravel应用程序中全局使用。 你可以组织你的帮助程序文件的位置，但是，我更愿意将我的Laravel项目帮助程序文件保存在 `app/Helpers/Helper.php` 中。 在本教程中，我们将在我想要的位置创建一个帮助文件。

### 创建一个帮助文件

你可以将你的帮助程序文件放在你的Laravel应用程序的任何位置，将它放在你的应用程序目录下是标准的。
让我们在app下创建一个 `Helpers` 目录并创建一个 `Helper.php` 文件。这些是该文件的以下内容。

```php
<?php

if (!function_exists('human_file_size')) {
    /**
     * Returns a human readable file size
     *
     * @param integer $bytes
     * Bytes contains the size of the bytes to convert
     *
     * @param integer $decimals
     * Number of decimal places to be returned
     *
     * @return string a string in human readable format
     *
     * */
    function human_file_size($bytes, $decimals = 2)
    {
        $sz = 'BKMGTPE';
        $factor = (int)floor((strlen($bytes) - 1) / 3);
        return sprintf("%.{$decimals}f", $bytes / pow(1024, $factor)) . $sz[$factor];

    }
}

if (!function_exists('in_arrayi')) {

    /**
     * Checks if a value exists in an array in a case-insensitive manner
     *
     * @param mixed $needle
     * The searched value
     *
     * @param $haystack
     * The array
     *
     * @param bool $strict [optional]
     * If set to true type of needle will also be matched
     *
     * @return bool true if needle is found in the array,
     * false otherwise
     */
    function in_arrayi($needle, $haystack, $strict = false)
    {
        return in_array(strtolower($needle), array_map('strtolower', $haystack), $strict);
    }
}
```

如果你正在使用一个类并且它的方法是你的助手，你可以用命名空间声明来启动这个文件。

```php
namespace App\Helpers;
```

如果你不使用命名空间声明，则这些函数将变为全局可用，你甚至可以在不指定名称空间的情况下使用它们。 所有Laravel内置的帮助函数都是在没有命名空间的情况下定义的。 此外，助手类也将在全局范围内提供。 因此，如果你想在不指定命名空间的情况下使用助手，只需删除这一行。

定义这些函数时有一些注意事项。所有的Laravel助手文件函数都会被检查以避免函数定义冲突。


```php
if (!function_exists('human_file_size')) {
    function human_file_size($bytes, $decimals = 2)
    {
        // ...
    }
}
```

如果跳过此检查，则每次重新定义具有相同定义的函数时都会发生冲突。你可以使用这个检查，或者你也可以用你的函数名称作为前缀来避免冲突。

### 使用帮助文件

现在，就我们的帮助文件而言，就是这样。让我们看看如何在Laravel应用程序中使用助手文件。

- 你可以使用 composer 自动加载助手文件。然后，你可以在应用程序的任何位置使用这些功能。
- 你也可以使用Laravel服务提供者注册此文件。 Laravel会将其与其他依赖关系一起加载。
- 你也可以使用一个包含所有这些功能的软件包。

让我们看看如何使用所有这些方法。

#### 通过Composer自动加载

第一个非常简单直接。 只需转到位于Laravel项目中的 `composer.json` 文件，你将看到自动加载 key。 Composer 有一个key `files`（你想自动加载的文件路径数组），你可以在自动 `autoload 中使用它。 如：

```json
"autoload": {
    "files": [
        "app/Helpers/Helper.php"
    ],
    "classmap": [
        "database/seeds",
        "database/factories"
    ],
    "psr-4": {
        "App\\": "app/"
    }
},
```

更改composer.json文件并向文件数组添加新路径后，需要重新生成自动加载文件。只需从Laravel项目目录中的终端运行此命令即可。

```bash
composer dump-autoload
```

现在，你的帮助程序文件将自动加载到你的Laravel项目中。

#### 通过服务提供者加载

让我们来看看如何使用服务提供者来自动加载助手文件。转至应用程序根目录中的命令行并运行以下命令以创建新的服务提供者。

```bash
php artisan make:provider HelperServiceProvider
```

将会提示运行结果

```bash
Provider created successfully.
```

一旦服务提供者成功创建，打开该文件。在注册方法中添加你的助手文件。

```php
public function register()
{
    $file = app_path('Helpers/Helper.php');
    if (file_exists($file)) {
        require_once($file);
    }
}
```

在注册方法中，我们包含了我们的依赖关系。 在大型项目中，你可能在目录中有多个帮助程序文件，并且你想要全部这些文件。 你可以更改注册方法，如下所示，你的服务提供商将加载Helpers目录中的所有文件。

```php
public function register()
{
    foreach (glob(app_path() . '/Helpers/*.php') as $file) {
        require_once($file);
    }
}
```

它将需要 `app/Helpers` 目录中的所有文件。
现在我们的服务提供者已经完成，我们需要注册我们的服务提供者，所以，Laravel会在引导期间加载它。 为此，请转至 `config/app.php` 并在结尾处的 `providers` 数组中添加以下行。

```php
App\Providers\HelperServiceProvider::class,
```

如果你的帮助文件涉及到一个拥有这些帮助方法的类，并且你已经指定了命名空间，那么你可以通过定义一个别名来毫不费力地使用它们。 您可以通过在 `config/app.php` 文件中的别名数组末尾添加以下内容轻松完成此操作。

```php
'Helper' => App\Helpers\Helper::class,
```

通过将这添加到别名数组中，你将能够使用Helper关键字调用助手。这就是为服务提供者创建你的帮手。

#### 使用第三方包加载

你也可以使用第三方package: [Laravel helpers package](https://github.com/browner12/helpers/)。你可以通过在控制台中从你的应用程序的根目录运行此命令来通过编写器安装它。

```bash
composer require browner12/helpers
```

在 `config/app.php` 中的 `providers` 数组中添加以下行

```php
browner12\helpers\HelperServiceProvider::class,
```

如果你正在使用Laravel的自动包发现功能，则可以跳过此步骤。 完成必要的步骤之后，你可以使用此命令创建助手文件。

```bash
php artisan make:helper Helper
```

它将在 `App\Helpers` 中创建一个 `Helper.php` 文件，你可以轻松添加所有帮助程序功能。

### Helper 的具体使用

现在我们的函数在Helper文件中定义，没有定义名称空间，我们可以很容易地使用它们。 只需在 `routes/web.php` 中找到你的路由文件，并将此功能用于首页。 例如，这是完整的 `routes/web.php` 文件：

```php
<?php

Route::get('/', function () {
    return human_file_size(1024*1024);
});
```

它将简单地返回作为参数传递的字节数的可读大小。你可以从任何地方控制器或视图调用这些函数。

## 资源

- 查看所有可用的 [内置Laravel助手](https://laravel.com/docs/helpers)
- 一个简单的包来创建助手文件 [browner12/helpers](https://github.com/browner12/helpers)
- 了解有关使用 [Composer Autoloader](https://getcomposer.org/doc/04-schema.md#autoload) 的更多信息

欢迎留言讨论。

> 原文地址: [https://tutsforweb.com/creating-helpers-laravel/](https://tutsforweb.com/creating-helpers-laravel/)