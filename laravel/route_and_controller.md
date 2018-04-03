

## 简介

任何Web应用框架必要的功能就是获取用户的请求并返回一个响应， 一般是通过HTTP(S)。也就意味着当学习一个Web框架的时候，定义一个应用的路由是第一位的，同时也是要去解决的最重要的部分， 没有路由，你是没法和终端用户交互的。

在本文，我们回去学习Laravel的路由，看看如何定义它们，如何把它们指到要执行代码的地方，怎么样使用Laravel的路由工具来处理不同的路由。

## 路由定义

在Laravel应用里，`Web`相关的路由定义在`routes/web.php`里，`API`相关的路由定义在`routes/api.php`里。Web路由会被终端用户访问到，而`API`路由主要是应用与API，比如你有一个移动客户端。接下来的内容，我们主要关注在web路由 `routes/web.php`。

> 如果Laravel版本早于5.3， 只有一个路由文件 `app/Http/routes.php`

定义一个路由最简单的方式就是匹配一个带有闭包的根路径(比如： `/`)，比如

基本路由定义
```
// routes/web.php
Route::get('/', function() {
    return 'Hello, World!';
})
```

> 什么是闭包？
> 闭包是PHP的匿名函数版本。更详细的说闭包是一个可以作为对象传递，分配给变量，作为参数传递给其他函数和方法，甚至可以序列化。

现在你已经定义了一个路由，如果有人访问 `/`， Laravel有路会执行定义好的闭包并返回结果。 注意这里是 `return` 内容而不是  `echo` 或 `print`。

> 快速了解中间件
> 你可能想要知道，为什么是 `return` `Hello World!` 代替 `echo`。
> 这里也有不少答案，但是最简单的是Laravel的请求和响应周期有很多包装，包括中间件。当路由闭包或者控制器方法执行完的时候，还没有时间将输出发送到浏览器；返回内容允许它在返回给用户之前继续流过响应堆栈和中间件。

许多简单的网站可以完全定义在web路由文件里。一些简单的`GET`路由也可以和模板组合着用，你可以很容易的启动一个网站，比如

简单网站
```
Route::get('/', function () { 
    return view('welcome');
});

Route::get('about', function () { 
    return view('about');
});

Route::get('products', function () {
    return view('products'); 
});

Route::get('services', function () { 
    return view('services');
});
```

> 关于静态调用
> 如果你用PHP开发有一些经验， 你看到对于路由类的静态调用可能有一些惊讶。实际上这不是一个静态方法，而是使用了Laravel的`facade`， 后面会有文章专门来介绍。
> 如果你不喜欢facade， 你也可以完成相同的定义，如
> ```
> $router->get('/', function () { 
>    return 'Hello, World!';
> });
> 

### 路由动作

你可能注意到我们已经在路由定义里使用过 `Route::get`。这意思就是我们告诉Laravel当有一个HTTP请求使用`GET`时只能匹配到这条路由。那如果是form的 `POST`, 或者是JavaScript发送了 `PUT` 或者 `DELETE` 请求呢？ 下面有一些在路由定义时可以用的其他选项。

路由动作
```
Route::get('/', function(){
    return 'Hello, World!';
});

Route::post('/', function(){});

Route::put('/', function(){});

Route::delete('/', function () {}); 

Route::any('/', function () {}); 

Route::match(['get', 'post'], '/', function () {});
```

### 路由处理

正如你可能已经猜到的那样，将闭包传递给路由定义并不是教导它如何解决路由的唯一方法。闭包是快速且简单，但是应用程序越大，将所有路由逻辑放在一个文件中变得越笨拙。另外，使用路由闭包的应用也不能使用Laravel路由缓存，还可以为每个请求减少几百毫秒。

另一个常用的选项是传递一个控制器和方法名作为一个字符串代替闭包，例如

```
Route::get('/', 'WelcomeController@index');
```

该命令是告诉Laravel把请求传给 `App\Http\Controllers\WelcomeController` 控制器的 `index()` 方法。而且也可以传递与闭包相同的参数。

### 路由参数

如果你定义的路由有参数，在路由中定义它们并把它们传递给闭包也是非常简单的。

路由参数
```
Route::get('users/{id}/friends', function($id) {
    // your code
});
```


关于 **路由参数和闭包/控制器里方法参数之间的命名关系** 这里要多说一下，可能很多人对这里都会有些疑问。

如你看到上面这个路由参数的例子，它是非常常见的，用于路由参数(`{id}`)和注入到路由定义里(`function($id)`)的方法参数使用相同的参数名。但是这有必要这么做么？

除非你使用路由/模型绑定, 否则是没有必要的。定义的路由参数匹配哪一个方法参数唯一要注意的是他们的顺序(从左到右)，看下面的例子

```
Route::get('users/{userId}/comments/{commentId}', function ( 
    $thisIsActuallyTheUserId,
    $thisisReallyTheCommentId
){
    //
});
```

我推荐它们保持一致，通过命名相同可以减少错误。 // todo

通过在参数名后面使用`?`也可以让路由参数变为可选。看下面的代码，这个case中，给参数提供了一个默认值。

可选的路由参数
```
Route::get('users/{id?}', function ($id = 'fallbackId') { 
    //
});
```

你也可以使用正则表达式来定义一个路由，该路由只有参数满足实际的要求才会匹配，如

正则表达式路由
```
Route::get('users/{id}', function ($id) { 
    //
})->where('id', '[0-9]+');

Route::get('users/{username}', function ($username) { 
    //
})->where('username', '[A-Za-z]+'); 

Route::get('posts/{id}/{slug}', function ($id, $slug) {
    //
})->where(['id' => '[0-9]+', 'slug' => '[A-Za-z]+']);
```

如果你访问的路径匹配到了一个路由，但是正则表达式不匹配参数，那最终也不会匹配到。由于路由匹配是从上到下的，所以 `/users/abc`会跳过第一个路由，但是会匹配到第二条闭包路由。另一方面 `posts/abc/123` 不会匹配到任何一条路由，所以会返回 `404 Not Found`的错误。

### 路由名称

在应用中使用这些路由最简单的方式就是只使用他们的路径(`path`)。`url()` helper 可以简化视图里的链接。看下面的例子，helper 会给路由前面加上网站的完整域名。

URL helper
```
<a href="<?php echo url('/'); ?>">
// outputs <a href="http://myapp.com/">
```

而且Laravel还允许你给每个路由起一个名字，使你可以在不明确引用URL的情况下使用它。这很有帮助，因为这意味着你可以给复杂的路由提供简单的昵称，并且因为按名称链接它们意味着如果路径更改，则不必重写前端链接。

定义路由名称
```
// Defining a route with name in routes/web.php:
Route::get('members/{id}', 'MembersController@show')->name('members.show'); 

// Link the route in a view using the route() helper
<a href="<?php echo route('members.show', ['id' => 14]); ?>">
```

这个例子也提到了几个新的概念。首先是可以通过在 `get()` 方法之后通过链式方法 `name()` 使用熟练的路由定义来添加名称。然后这个方法允许我们指定一个简短的别名，来在其他地方更容易的使用。

我们命名路由为 `members.show`， 资源复数 在Laravel的路由和视图名也是常用的规范。

**关于路由命名规范**

你可以命名路由按照你喜欢的方式，但是常用到的规范是使用资源名称复数，然后使用一个点 `.`, 然后是具体的动作。这里有一组给图片资源命名的最常用到的路由名称：

```
    photos.index
    photos.create
    photos.store
    photos.show
    photos.edit
    photos.update
    photos.destroy
```

我们也介绍过 `route()` helper, 就像 `url()` 一样，它的目的是在视图中使用，以简化与命名路由的链接。如果路由里没有参数，你可以简单的传一个路由名 `route('members.index')` ，然后会收到一个路由字符串 `http://myapp.com/members/index`。如果有参数，以数组的形式传入第二个参数里。

一般情况下，我推荐使用路由昵称来代替使用路由路径，因此使用 `route()` 代替 `url()`。

**关于传递路由参数到route()**

当路由有参数的时候(比如: `users/{id}`), 在使用 `route()` 生成链接给路由的时需要定义这些参数。

传递参数有几种不同的方式，设想一个路由是 `users/{userId}/comments/{commentId}`。 如果用户ID是1，评论ID是2，来看下我们可以使用的几种方式：

方式1：
```
route('users.comments.show', [1, 2]) 
// http://myapp.com/users/1/comments/2
```

方式2：
```
route('users.comments.show', ['userId' => 1, 'commentId' => 2]) 
// http://myapp.com/users/1/comments/2
```

方式3：
```
route('users.comments.show', ['commentId' => 2, 'userId' => 1]) 
// http://myapp.com/users/1/comments/2
```

方式4：
```
route('users.comments.show', ['userId' => 1, 'commentId' => 2, 'opt' => 'a']) 
// http://myapp.com/users/1/comments/2?opt=a
```

如你所见，没有key的数组是需要按顺序指定的，带有key的数组通过和路由里的key进行匹配，剩下的会被作为查询参数。

## 路由组

通常，一组路由共享一个特定的特征 - 特定的身份验证要求，路径前缀或控制器命名空间。在每一条路由里一遍又一遍的定义这些共享的特征不仅看起来乏味，而且可能会玷污你的路由文件，并且掩盖应用程序的某些结构。

路由组允许将几个路由组合到一起，应用共享的配置到整个路由组一次，减少重复。另外，路由组也是给未来开发者的一个可以看的见的线索。

要讲两个或更多个路由组合到一起，你要围绕一组路由。如下

定义一组路由
```
Route::group([], function(){
    Route::get('hello', function() {
        return 'Hello';
    });

    Route::get('world', function() {
        return 'world';
    })
});
```

默认情况下，路由组实际上不会做任何事情。上面的代码和之前的路由基本没啥区别。其中第一个参数是一个空数组，允许传入多重配置参数去应用到整个路由组。

### 中间件

可能对路由组最常用的就是应用一个中间件到一组路由组。后面会专门写一片关于中间件的文章。除此之外，它们是Laravel用于验证用户身份并限制访客使用网站的某些部分的内容。

下面的例子，是我们创建了一个路由组，包含 `dashboard` 和 `account` 视图，应用 `auth` 中间件到这两个路由。在这个例子中也就意味着用户必须登录到应用里来看 `dashboard` 和 `account` 页。

只有登录用户可以访问的路由组
```
Route::group(['middleware' => 'auth'], function(){
    Route::get('dashboard', function(){
        return view('dashboard');
    });

    Route::get('account', function(){
        return view('account');
    })
});
```

**关于在控制器中使用中间件**

经常在控制器中使用路由中间件比在路由定义里可能更清晰，更直接。你可以在控制器的构造方法里使用 `middleware()` 方法来做到。给 `middleware()` 传递的参数就是中间件的名称。你也可以链式调用一些方法(比如: `only()`, `except()`) 来定义哪些方法将接受该中间件。

```

class DashboardController extends Controller {
    public function __construct() 
    {
       $this->middleware('auth');

       $this->middleware('admin-auth')
            ->only('admin');
        
        $this->middleware('team-member')
           ->except('admin');
} }
```

请注意，如果你大量的使用 `only` 和 `except`, 以后会很难维护，所以不建议大面积这么使用。

### 路径前缀

如果你有一组路由要共享一个path， 比如你网站的API是以 `/api`开头的，那就可以使用路由组来指定这个结构，如

前缀路由组
```
Route::group(['prefix' =>'api'], function(){
    Route::get('/', function(){
        // handle the path /api
    });

    Route::get('users', function(){
        // handle the path /api/users
    });
});
```

> 注意每一个前缀路由组都会有一个 `/` 路由来表示根前缀，在这里就是 `/api`

### 子域名路由

子域名路由和路由前缀一样，但是它的作用域是子域名而不是路由前缀。有两种主要的使用方法。

第一种，你可以让整个应用分为不同的几个部分(或不同的应用)到不同的子域名，来看下怎么实现

子域名路由
```
Route::group(['domain' => 'api.myapp.com'], function(){
    Route::get('/', function(){
        //
    });
});
```

第二种，你也可以将子域名的一部分作为参数，最常见的例子就是slack，比如每一个公司都有自己的子域名 `phpcasts.slack.co`。

参数化的子域名
```
Route::get(['domain' => '{account}.myapp.com'], function(){
    Route::get('/', function($account) {
        //
    });

    Route::get('users/{id}', function($account, $Id){
        //
    });
});
```

> 注意，路由组里的任何一个参数都可以传入到组里的路由方法里作为第一个参数，如果是多个就依次写。

### 命名空间前缀

### 名称前缀

## 视图

## 控制器

## 路由模型绑定

## 路由缓存

## form方法欺骗

## 跳转

## 自定义响应

## 单元测试

## 总结





