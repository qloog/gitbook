
与大多数其他后端语言相比，PHP实际上作为模板语言运行得相当好。 但它有它的缺点，而且在整个地方使用<？php也很难，所以你可以期望大多数现代框架提供模板语言。

Laravel提供了一种名为 Blade 的定制模板引擎，该引擎受.NET的Razor引擎启发。 它拥有简洁的语法，较浅的学习曲线，强大而直观的继承模型以及易扩展性。

来快速了解下

```
<h1>{{ $group->title }}</h1>
{!! $group->heroImageHtml() !!}

@forelse ($users as $user)
    • {{ $user->first_name }} {{ $user->last_name }}<br>
@empty
    No users in this group.
@endforelse
```

正如您所看到的，Blade引入了一种惯例，其中自定义标记（`"directives"`称为指令）前缀为 `@`。 你将为所有控件结构使用指令，并为继承和你要添加的任何自定义功能使用指令。

Blade的语法简洁明了，与其他选择相比，它的工作更加愉快和整洁。 但是当你需要模板中任何复杂的任何东西的时候 - 嵌套继承，复杂条件或递归 - Blade 开始真正发光。 就像最好的Laravel组件一样，它需要复杂的应用要求，并且使它们变得简单易用。

此外，由于所有的Blade语法都被编译为正常的PHP代码并进行缓存，所以速度很快，并且它允许你在Blade文件中使用本地PHP（如果需要的话）。 但是，我建议尽可能避免使用PHP，通常如果你需要做任何你无法使用Blade或自定义Blade指令的任何操作，则它不属于模板。

## 展示数据

从前面那个例子可以看到，`{{` 和 `}}` 用于包装你想要回显的PHP部分。 `{{ $variable }}` 与普通PHP中的 `<?= $variable ?>`类似。

然而，它有一种不同之处，你可能已经猜到了这一点：Blade默认使用PHP的 `htmlentities()` 来保护用户免受恶意脚本攻击的影响。 这意味着 `{{ $variable }}` 在功能上等价于 `<?= htmlentities($variable) ?>`。 如果你想在没有转义的情况下回显，请使用 `{!!` 和 `!!}`。

## 控制结构

Blade中的大部分控制结构都非常熟悉。 许多人直接在PHP中回显相同标签的名称和结构。
Blade提供了一些方便的helper，但总的来说，控制结构看起来比在PHP中看起来更清洁。

### 条件

首先，让我们看看允许逻辑判断的控制结构。

#### @if 

Blade 的 `@if ($condition)` 会编译为 `<?php if ($conditin): ?>`。`@else`, `@elseif` 和 `@endif` 也一样会被编译为同样PHP原生的标签。 来看下面的例子：

*@if, @elsf, @elseif, @endif*
```
@if (count($users) === 1)
    // 这里只有一个用户
@elseif (count($users) === 0)
    // 一个用户也没有
@else
    // 同有 {{ count($users) }} 个用户
@endif
```

就像使用原生PHP条件控制，你也可以混合使用它们。它们没有什么特殊的逻辑，解析器只是会寻找 `@if ($condition)` 代码段，然后使用原生PHP代码来替换它。

#### @unless and @endunless

`@unless`，算是一个新语法，因为在原生PHP里并没有该功能。它和 `@if` 作用相反。 `@unless ($condition)` 和 `<?php if(!$condition)?>` 作用一样。看例子就明白了。

*@unless和@endunless*
```
@unless ($user->hasPaid())
    // 你已经完成支付
@endunless
```

### 循环

#### @for，@foreach, @while

Blade中的 `@for`,`@foreach`,`@while` 和原生PHP中的是一样的。

*@for and @endfor*
```
@for ($i = 0; $i < $user->postCount(); $i++) 
    文章总数是 {{ $i }}<br>
@endfor
```

*@foreach 和 @endforeach*
```
@foreach ($posts as $post)
    {{ $post->title }} ({{ $post->view_count }} )<br>
@endforeach
```

*@while 和 @endwhile*
```
@while ($item = array_pop($items)) 
    {{ $item->orSomething() }}<br>
@endwhile
```

#### forelse

`@forelse` 和 `@foreach` 基本一致。只是当循环的对象为空时，可以使用 `@empty` 来处理。具体看例子

*@forelse*
```
@foreach ($posts as $post)
    {{ $post->title }} ({{ $post->view_count }} )<br>
@empty
   还没发过文章
@endforelse
```

> **关于 `@foreach` 和 `@forelse` 中使用@loop**
> @loop 指令是在Laravel5.3版本开始添加的，PHP原生语言并不支持改特性，在 @foreach 和 @forelse 循环里可以使用 `$loop`，该变量会返回一个 `stdClass` 对象。来看下面的例子以便自增强理解：

```
<ul>
@foreach ($pages as $page)
   <li>{{ $loop->iteration }} : {{ $page->title }} 
       @if ($page->hasChildren())
       <ul>
       @foreach ($page->children() as $child)
           <li>{{ $loop->parent->iteration }}.
               {{ $loop->iteration }}:
               {{ $child->title }}
           </li>
       @endforeach 
       </ul> 
       @endif
   </li> 
@endforeach
</ul>
```

### or

如果你不确定一个变量是否被set，你可能会使用 `isset()` 来进行判断，如果没有设置则显示其他内容。Blade 为我们提供了一个helper,让我们可以很简单的来做这件事。那就是 `or` ：`{{ $title or 'no set title'}}`。意思就是设置了显示 `$title`，没设置则显示 'no set title'。

## 模板继承

Blade为模板继承提供了一个结构，允许视图扩展，修改并包含其他视图。以下是Blade如何构建继承。

### 用 `@section/@show` 和 `@yield` 定义section

让我们从顶层Blade布局开始，如下面的例子。 这是通用页面的布局定义，我们稍后将把页面特定的内容放入到对应的section。

*Blade布局*
```
<!-- resources/views/layouts/master.blade.php -->
<html>
    <head>
        <title>My Site | @yield('title', 'Home Page')</title> 
    </head>
    <body>
        <div class="container">
            @yield('content')
        </div>
        
        @section('footerScripts')
            <script src="app.js"></script> 
        @show
    </body>
</html>
```

这看起来有点像普通的HTML页面，但你可能已经看到过 `yield`在两个地方(title和content)，还定义了一个 `section`。
这三个Blade指令看起来都有点不同，但本质都是相同的。第一个参数是section的名称，也都可以被继承，没有默认值的时候有的也可以设置默认值。

那不同的是什么呢？很明显 `@yield('content')` 没有默认值，但 `@yield('title')` 如果没有被继承，将会显示默认值。如果被继承，子section则不会有可访问的默认值。 `@seciton ... @show` 定义了一个默认值，子模板可以通过 `@parent` 来访问父模板的默认内容。

*继承Blade布局模板*
```
<!-- resources/views/dashboard.blade.php -->
@extends('layouts.master')

@section('title', 'Dashboard')

@section('content')
    Welcome to your application dashboard!
@endsection

@section('footerScripts')
    @parent

    <script src="dashboard.js"></script> @endsection
```

> *@show VS @endsection*
> 你可能注意到了例子中的 `@section ... @show` 和 `@section ... @endsection`。那它们有什么不同呢？
>
> 当在父模板中定义section的时使用 `@show`。 在为子模板中的模板定义内容时使用 `@endsection`。

#### @extends 

首先，用 `@extends('layouts.master')`，我们定义这个视图不应该单独渲染，而是扩展另一个视图。 这意味着它的作用是定义各个部分的内容，而不是独立。 它几乎更像一个系列的容器，而不是HTML页面。 这一行还定义它扩展的视图位于 `resources/views/layouts/master.blade.php`。每个文件只能扩展一个文件，并且 `@extends` 调用应该是文件的第一行。

#### @section and @endsection

其次，通过 `@section('title'，'Dashboard')`，我们为第一部分标题提供的内容。 由于内容非常短，我们只是使用快捷方式，而不是使用 `@section` 和 `@end` 部分。 这允许我们将内容作为 `@section` 的第二个参数传递，然后继续。 如果看到没有 `@endsection` 的 `@section` 有点令人不安，那么你可以使用正常的语法。

第三，使用 `@section('content')`，我们使用正常的语法来定义section的内容。 但是，请注意，当在子视图中使用 `@section` 时，可以使用 `@endsection`（或其别名 `@stop` ）而不是 `@show` 来保存该视图，该视图保留用于在父视图中定义部分。

### @parent

使用 `@section('footerScripts')` 开启，我们使用正常语法来定义footerScripts部分的内容。
但请记住，实际上已经在主布局中定义了内容（至少是其“默认”）。 所以这一次，我们有两个选择：我们可以覆盖父视图的内容，或者可以添加它。我们可以选择在部分中使用 `@parent` 指令来包含来自父级的内容。 如果不这样做，本节的内容将完全覆盖本节中为父节点定义的任何内容。

### @inclde

既然我们已经建立了继承的基础知识，那么我们可以执行更多的技巧。
如果有一个视图并希望引入另一个视图呢？ 那就可以使用 `@include`, 来看实际的例子

*用@include包含子视图*
```
<!-- resources/views/home.blade.php -->
<div class="content" data-page-name="{{ $pageName }}">
    <p>Here's why you should sign up for our app: <strong>It's Great.</strong></p>
    @include('sign-up-button', ['text' => 'See just how great it is'])
</div>

<!-- resources/views/sign-up-button.blade.php -->
<a class="button button--callout" data-page-name="{{ $pageName }}"> 
    <i class="exclamation-icon"></i> {{ $text }}
</a>
```

从例子中我们发现 @include 的第二个参数是可以传递变量到被包含的页面。

### @each

您可能可以想象在某些情况下您需要遍历数组或集合，并为每个项目包含一个部分。 有一个指令：`@each`。
假设我们有一个由模块组成的边栏，我们希望包含多个模块，每个模块都有不同的标题。 看例子。

```
<!-- resources/views/sidebar.blade.php -->
<div class="sidebar">
    @each('partials.module', $modules, 'module', 'partials.empty-module')
</div>

<!-- resources/views/partials/module.blade.php -->
<div class="sidebar-module"> 
    <h1>{{ $module->title }}</h1>
</div>

<!-- resources/views/partials/empty-module.blade.php -->
<div class="sidebar-module"> 
    No modules :(
</div>
```

考虑 `@each` 语法。 第一个参数是视图部分的名称。 第二个是要迭代的数组或集合。 第三个是每个项目（在这种情况下，`$modules` 数组中的每个元素）将被传递给视图的变量名称。 可选的第四个参数是显示数组或集合是否为空的视图（或者，你可以选择传递一个字符串作为模板）。

## 视图设计和注入

之前的文章我们提到过，通过路由定义将数据传递给我们的视图很简单, 如

```
Route::get('passing-data-to-views', function() { 
    return view('dashboard')
        ->with('key', 'value');
});
```

然而，有时候，你会发现自己一遍又一遍地传递相同的数据到多个视图。 或者，你可能会发现自己使用的标题部分或类似的东西需要一些数据; 你是否必须从加载该头部分的每个路由定义中传递该数据？

### 绑定数据到视图使用视图组装

谢天谢地，有一种更简单的方法。 该解决方案称为视图组装，它允许你定义在特定视图加载时，应该将某些数据传递给它，而无需显式地传递该数据。
假设你在每个页面上都有一个侧栏，定义在一个名为 `partials.sidebar(resources/views/partials/sidebar.blade.php)`的partials，然后包含在每个页面上。 该侧边栏显示了你网站上发布的最近七个帖子的列表。 如果它在每个页面上，每个路由定义通常必须抓取该列表并传入，如：

```

Route::get('home', function () { 
    return view('home')
        ->with('posts', Post::recent());
});

Route::get('about', function () { 
    return view('about')
        ->with('posts', Post::recent());
});
```

这可能会很快变得烦人。 相反，我们将使用视图设计将该变量与一组规定的视图共享。 我们可以用几种方法做到这一点，来一一看下

#### 共享全局变量

第一种，就是在应用的每一个视图里使用一个全局共享的变量。

```
// 在 provider 文件里
public function boot() 
{
    ...
    view()->share('posts', Post::recent());
}
```

如果你想使用 `view()->share()`，最好的地方应该是在服务提供者的 `boot()` 方法里，以便绑定在每个页面加载时运行。 你可以创建一个自定义的 `ViewComposerServiceProvider`，但现在只需将它放在 `App\Providers\AppServiceProvider` 方法中的 `boot()` 即可。
这样，使用 `view()->share()` 就可以让整个应用程序中的每个视图都可以访问该变量。

#### 基于闭包的视图组装

其次是使用基于闭包的视图组合来共享单个视图的变量，如下。

```
view()->composer('partials.sidebar', function ($view) { 
    $view->with('posts', Post::recent());
});
```

我们已经在第一个参数（partials.sidebar）中定义了我们想要共享的视图的名称，然后将闭包传递给第二个参数; 在闭包中，我们使用 `$ view->with()` 来共享一个变量，但现在只能使用特定的视图。

> *多视图使用视图组装*
> 任何一个视图组装都可以绑定到一个特定的视图，也可以传递一个视图名称数组来绑定到多个视图。也可以在视图路径中使用星号，如 `partials.*`，`tasks.*` 或仅 `*`：

```
view()->composer(
['partials.header', 'partials.footer'], 
    function () {
        $view->with('posts', Post::recent());
    }
);

view()->composer('partials.*', function () { 
    $view->with('posts', Post::recent());
});
```

#### 基于类的视图组装

最后，最灵活也最复杂的选择是为视图组装创建一个专门的类。
首先，我们来创建视图组合类。 视图组合没有正式定义的地方，但文档推荐 `App\Http\ViewComposers`。 因此，让我们创建 `App\Http\ViewComposers\RecentPostsComposer`。

```
<?php

namespace App\Http\ViewComposers;

use App\Post;
use Illuminate\Contracts\View\View;

class RecentPostsComposer 
{
    private $posts;

    public function __construct(Post $posts) 
    {
        $this->posts = $posts;
    }

    public function compose(View $view) 
    {
        $view->with('posts', $this->posts->recent());
    }
}
```

从示例中可以看到，我们注入了一个Post模型。 请注意，我们可以跳过私有的 `$post` 和构造函数注入，如果需要的话，可以在 `compose()`方法中使用 `Post::recent()`。 然后，当这个组合被调用时，它会运行 `compose()`方法，在这个方法中，我们将 posts变量绑定到运行 `recent()` 方法的结果。

和共享变量的其他方法一样，这个视图组合需要在某个地方绑定。 再次，你可能会创建一个自定义的 `ViewComposerServiceProvider`，但现在，如下示例所示，我们将它放在 `App\Providers\AppServiceProvider` 的 `boot()`方法中。

```
// AppServiceProvider
public function boot() 
{
    ...
    view()->composer(
        'partials.sidebar',
        \App\Http\ViewComposers\RecentPostsComposer::class
    ); 
}

```

请注意，该绑定与基于闭包的视图组合相同，但不是传递闭包，而是传递视图组合的类名称。 现在，每次Blade渲染 `partials.sidebar` 视图时，它都会自动运行我们的提供程序，并将视图post变量设置为Post模型上 `recent()`方法的结果。

### Blade服务注入

## 自定义Blade directives

### 自定义 directives 的参数

### 示例：使用自定义 directives

## 总结