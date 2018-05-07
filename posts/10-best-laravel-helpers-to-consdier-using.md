# 10个非常好用的 Laravel 辅助函数

Laravel包含各种全局辅助函数(helper), 你可以使用它们使开发工作流程更加轻松。 在这里，我将会列一下10个最好的 Laravel helpers，它们使我的开发更容易。 必要时大家也考虑使用它们。

更多的辅助函数，可以看 [Laravel官方文档](https://laravel.com/docs/helpers)

## array_dot()

`array_dot()` 辅助函数允许你将多维数组转换为使用点符号的一维数组。

```php
$array = [
    'user' => ['username' => 'something'],
    'app' => ['creator' => ['name' => 'someone'], 'created' => 'today']
];

$dot_array = array_dot($array);

// [user.username] => something, [app.creator.name] => someone, [app.created] => today
```

## array_get()

`array_get()` 函数使用点符号从多维数组中检索值。

```php
$array = [
    'user' => ['username' => 'something'],
    'app' => ['creator' => ['name' => 'someone'], 'created' => 'today']
];

$name = array_get($array, 'app.creator.name');

// someone
```

如果key不存在，array_get（）函数还接受可选的第三个参数作为默认值。

```php
$name = array_get($array, 'app.created.name', 'anonymous');

// anonymous
```

## public_path()

`public_path()` 返回 Laravel 应用程序中公共目录的完全限定的绝对路径。 你还可以将路径传递到公共目录中的文件或目录以获取该资源的绝对路径。 它将简单地将`public_path()` 添加到你的参数中。

```php
$public_path = public_path();

$path = public_path('js/app.js');
```

## Str::orderedUuid()

`Str::orderedUuid()` 函数首先生成一个时间戳uuid。 这个uuid可以存储在索引数据库列中。 这些uuid是基于时间戳创建的，因此它们会保留你的内容索引。 在Laravel 5.6中使用它时，会引发 `Ramsey\Uuid\Exception\UnsatisfiedDependencyException`。 要解决此问题，只需运行以下命令即可使用 `moontoast/math` 包：

```shell
composer require "moontoast/math"
```

```php
use Illuminate\Support\Str;

return (string) Str::orderByUuid()

// A timestamp first uuid
```

## str_plural()

`str_plural()` 函数将字符串转换为复数形式。该功能只支持英文。

```php
echo str_plural('bank');

// banks

echo str_plural('developer');

// developers
```

## route()

`route()` 函数为指定的路由生成路由URL。

```php
$url = route('login');
```

如果路由接受参数，你可以简单地将它们作为第二个参数传递给一个数组。

```php
$url = route('products', ['id' => 1]);
```

如果你想产生一个相对的URL而不是一个绝对的URL，你可以传递false作为第三个参数。

```php
$url = route('products', ['id' => 1], false);
```

## tap()

`tap()` 函数接受两个参数：一个值和一个闭包。该值将被传递给闭包，然后该值将被返回。闭包返回值无关紧要。

```php
$user = App\User::find(1);

return tap($user, function($user) {
    $user->update([
        'name' => 'Random'
    ]);
});
```

它不会返回布尔值，而是返回 `User Model` 。

如果你没有传递闭包，你也可以使用 `User Model` 的任何方法。 无论实际返回的方法如何，返回值都将始终为值。 在下面的例子中，它将返回 `User Model` 而不是布尔值。 更新方法返回布尔值，但由于用了 `tap` ，所以它将返回 `User Model`。

```php
$user = App\User::find(1);

return tap($user)->update([
    'name' => 'SomeName'
]);
```

## dump()

`dump()` 函数会dump给定的变量，同时也支持同时传入多个变量。这对调试非常有用。

```php
dump($var1);
dump($var1, $var2, $var3);
```

## str_slug()

`str_slug()` 函数根据给定的字符串生成一个友好的URL。你可以使用此功能为你的帖子或产品标题创建一个 `slug` 。

```php
$slug = str_slug('Helpers in Laravel', '-');

// helpers-in-laravel
```

## optional()

`optional()` 函数接受一个参数，你可以调用它的方法或访问属性。如果传递的对象为null，则方法和属性将返回null而不是导致错误或抛出异常。

```php
$user = User::find(1);

return optional($user)->name;
```

> 原文地址： https://tutsforweb.com/10-best-laravel-helpers/