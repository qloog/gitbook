# 前端组件

Laravel 主要是一个PHP框架，但也提供了一系列的关注生成前端代码的组件。像分页和消息都是偏向前端的一些PHP helper，但 Laravel 也提供了一个基于 `Glup` 叫做 `Elixir` 的构建系统和一些非PHP 资产的约定。

## Elixir

Elixir（不要与功能性编程语言混淆）是一个构建工具，它提供了一个简单的用户界面和一系列Gulp上的约定。Elixir的核心功能是通过更简洁的API和一系列命名和应用程序结构约定来简化最常见的Gulp任务。

> *关于Glup*
> Gulp是一个JavaScript工具，用于编译静态资产并协调构建过程的其他步骤。
> Gulp与Grunt，Rake或make相似 - 它允许您定义一个动作（在Gulp中称为“任务”）或每次构建应用程序时采取的一系列动作。 这通常包括运行像Sass或LESS这样的CSS预处理器，复制文件，连接和缩小JavaScript等等。
> Gulp，因此Elixir是基于流的想法。 大多数任务将首先将一些文件加载到流缓冲区中，然后该任务将转换应用于内容 - 对其进行预处理，缩小它，然后将内容保存到新文件中。

Elixir的核心是Gulp工具箱中的一个工具。 甚至没有Elixir文件这样的东西; 你会在你的 `gulpfile.js` 中定义你的Elixir任务。 但是他们看起来与普通的 Gulp 任务有很大的区别，你必须做很多的工作才能让它们开箱即用。
让我们看一个常见的例子：运行Sass来预处理你的CSS样式。 在正常的Gulp环境中，如

* 用Glup编译一个Sass文件 *

```javascript
var gulp = require('gulp'),
    sass = require('gulp-ruby-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    rename = require('gulp-rename'),
    notify = require('gulp-notify'),
    livereload = require('gulp-livereload'),
    lr = require('tiny-lr'),
    server = lr();

gulp.task('sass', function() {
    return gulp.src('resources/assets/sass/app.scss')
        .pipe(sass({
            style: 'compressed',
            sourcemap: true
        }))
        .pipe(autoprefixer('last 2 version', 'ie 9', 'ios 6'))
        .pipe(gulp.dest('public/css'))
        .pipe(rename({suffix: '.min'}))
        .pipe(livereload(server))
        .pipe(notify({
            title: "Karani",
            message: "Styles task complete."
        }));
});
```

现在，我看到需要我们输入很多代码。 很容易阅读，而且很清楚发生了什么。 但是需要我们在每一个网站里都这样执行。 它会变得混乱和重复。
让我们在Elixir中尝试相同的任务。

```javascript
var elixir = require('laravel-elixir');
    elixir(function (mix) {
        mix.sass('app.scss');
});
```

我们看到简单的几行代码就搞定了和上面那一堆代码一样的功能，包含处理，通知，目录结构，自动前缀等等。

> * Elixir6 中使用ES6 *
> Elixir6中 是在Laravel5.3中引入的，做了很多修改，主要是使用ES6的语法，如下面的例子

```javascript
const elixir = require('laravel-elixir');

elixir(mix => {
   mix.sass('app.scss')
});
```

### Elixir 的目录结构

许多Elixir的简单性来自设定的目录结构。为什么要在每个新应用程序中确定源码和编译过后资产的位置呢？只要坚持Elixir的惯例，你就不必在考虑它了。

每个新的Laravel应用程序都带有一个 `resources` 目录，其中包含一个子目录 `assets`，这是Elixir存放前端资产的位置。你的 `Sass` 文件将会放在 `resources/assets/sass`，`LESS` 放于 `resources/assets/less` 中。`JavaScript`文件将会放在 `resources/assets/js`。这些文件最后会输出到 `public/css` 和 `public/js`。

如果你对更改目录机构感兴趣，则可以通过修改 `elixir.config` 对象的相应属性(`assetsPath`和`publicPath`)来更改源路径和公共路径。

### 运行 Elixir

由于 Elixir 运行在 Gulp之上，所以在使用前需要安装一些工具：

1. 首先，需要安装 Node.js。可以查看 [Node官方网站](https://nodejs.org)
2. 全局安装 Gulp，在终端里运行 `npm install --blobal gulp-cli`。一旦 Node 和 Gulp 安装好之后，以后就不用再安装这些命令了。只需要安装当前项目的一些依赖就可以了。
3. 在终端里，进入到项目的根目录，运行 `npm install` 安装所需要的包(Laravel项目默认已经自带了一个`package.json`)

安装完成后，就可以运行 `gulp`，`gulp watch` 用于监听文件的变化并在响应中做出变化，`gulp scripts` 和 `gulp styles` 只是为了运行脚本或样式。

### Elixir都提供了什么

我们已经提到过，Elixir 可以使用Sass或LESS来预处理CSS。它也可以合并文件，压缩文件，重命名文件，也可以复制整个目录或者单个文件。

Elixir 也可以处理 ES6/ES2015 的 JavaScript和 运行 Webpack， Rollup。不仅如此，现在大多数JavaScript和CSS的现代化编码标准都喊改在每个脚本或样式中，且都是开箱即用的。

Elixir也可以运行你的测试。 PHPUnit有一个方法，PHPSpec有一个方法; 既可以监听对测试文件的更改，又可以在每次进行任何更改时重新运行测试套件。

[Elixir 官方文档](https://laravel.com/docs/5.3/elixir) 涵盖了这些所有的选项，下面介绍一些经常使用到的。

> 注意从Laravel5.4开始Elixir已经改名为Mix, 但原来的功能基本都保留，同时新增了一些功能。

#### --production flag

默认情况下，Elixir 不会压缩有所的文件。除非你想要以`production`模式构建脚本，该模式会启动压缩，命令为

```bash
gulp --production
```

#### 传递多文件

通常接受单个文件的大部分Elixir方法（例如: `mix.sass('app.scss')`）也可以接收一组文件。

* 用Elixir组合多文件 *

```javascript
const elixir = require('laravel-elixir');

elixir(mix => {
    mix.sass([
        'app.scss',
        'public.scss'
    ]);
});
```

#### Source maps

默认情况下，Elixir会为你的文件生成一个source map，文件名看起来是 `.{filename}.map`。

如果您对source map不熟悉，他们可以使用任何类型的预处理器来告诉你的浏览器的Web检查器哪些文件生成你正在检查的编译源。

没有source map，如果你使用浏览器的开发工具来检查特定的CSS规则或JavaScript操作，你只会看到大量的编译代码。 使用source map，你的浏览器可以精确定位源文件的准确行，无论是Sass还是JavaScript或其他任何内容，都会生成你正在检查的规则。

如果不想使用source map，可以通过配置文件来修改

* 禁用source map *

```javascript
const elixir = require('laravel-elixir');
elixir.config.sourcemaps = false;
elixir(mix => {
    mix.sass('app.scss');
});
```

#### Preprocessorless CSS

如果你不想用预处理器处理，也可以使命令，它可以抓取你的所有CSS文件，合并，然后输出到 `public/css` 目录下。如果不想指定输出文件名，则默认是 `all.css`。当然还有更多的选项，看下面的例子：

* 组合样式 *

```javascript
elixir(mix => {
    // Combines all files from resources/assets/css and subfolders mix.styles();
    // Combines files from resources/assets/css
    mix.styles([
        'normalize.css',
        'app.css'
    ]);

    // Combines all styles from other directory
    mix.stylesIn('resources/some/other/css/directory');
    // Combines given styles from resources/assets/css // and outputs to a custom directory
    mix.styles([
        'normalize.css',
        'app.css'
    ], 'public/other/css/output.css');

    // Combines given styles from custom directory // and outputs to a custom directory mix.styles([
        'normalize.css',
        'app.css'
    ], 'public/other/css/output.css', 'resources/some/other/css/directory');
});

```

#### 合并JavaScript

可用于处理普通JavaScript文件的选项与普通CSS文件可用的选项非常相似。看下面的例子

* 合并Javascript *

```javascript
const elixir = require('laravel-elixir');

elixir(mix => {
    // Combines files from resources/assets/js
    mix.scripts([
        'jquery.js',
        'app.js'
    ]);

    // Combines all scripts from other directory
    mix.scriptsIn('resources/some/other/js/directory');

    // Combines given scripts from resources/assets/js
    // and outputs to a custom directory
    mix.scripts([
        'jquery.js',
        'app.js'
    ], 'public/other/js/output.js');

    // Combines given scripts from custom directory
    // and outputs to a custom directory
    mix.scripts([
        'jquery.js',
        'app.js'
    ], 'public/other/js/output.js', 'resources/some/other/js/directory');
});
```

#### 处理JavaScript

如果想要处理JavaScript，例如把 ES6 的代码编译为原生的JavaScript, Elixir 使用 Webpack 或 Rollup 可以很容易的做到。

* 在Elixir中用Webpack或Rollup处理JavaScript文件 *

```javascript
elixir(function(mix) { 
    mix.webpack('app.js');
    // or
    mix.rollup('app.js');
});
```

此脚本会在 `resources/assets/js` 目录中查找提供的文件名 `app.js`，然后输出到 `public/js/all.js`。

你也可以使用关于 `Webpack` 更复杂的一些特性，通过在项目根目录里创建 `webpack.config.js` 。
如果在早于 Laravel 5.3使用 Elixir的话，需要使用 `mix.browserify('app.js')` 来进行编译。

#### 版本

关于让网站变得更快其实已经进入到了我们日常开发实践。我们将脚本移动到页脚，减少HTTP请求的数量等等。早期的做法是在资产(脚本、样式和图像)上设置非常长的缓存时间。这意味着会有更少的请求来到服务器上获取资产的最新版本。如果我们对资产做了修改，用户是很难刷新他们的本地缓存。解决方案就是版本控制。每次运行构建脚本是，都会为每个资产的文件名添加一个唯一的散列值，然后该文艺文件奖杯无限缓存，或者至少在下一次构建之前。

这样做会有什么问题么？ 首先你需要获得生成的唯一哈希值并将其附加到文件名中。 但是你还需要更新每个构建的视图以引用新文件名。
Elixir为你处理这件事，而且它非常简单。 有两个组件：Elixir中的版本控制任务和 `elixir` PHP helper程序。 来看例子：运行`mix.version()` 来版本化资产。

```javascript
const elixir = require('laravel-elixir');

elixir(mix => {
    mix.version('public/css/all.css');
});
```

此脚本会对指定文件生成一个带有唯一哈希值的版本，同时放入 `public/build` 目录下，如 `public/build/css/all-2342dkf9.css`。

下面在视图中使用 `elixir` helper

```html
<link rel="stylesheet" href="{{ elixir("css/all.css") }}"> 

// 执行后
<link rel="stylesheet" href="/build/css/all-2342dkf9.css">
```

#### 测试

在每次测试文件改变的时候，用Elixir也可以很容易的运行单元测试和 PHPSpec测试。有两个选项: `mix.phpUnit()` 和 `mix.phpSpec()`，每次都会直接从 `vendor` 目录中各种运行，所以你不必对他们做任何的工作。

#### Elixir扩展

Elixir不仅为自己的预建任务提供简单的语法; 它也可以很容易地定义你自己的。

假设你想在某些点将文本保存到日志文件中。 用shell命令，它是 `echo "message" >> file.log`。 通常我们将它定义为一个Gulp任务，使用 `shell('echo “message” >> file.log')`，如:

```javascript
// Define the task
gulp.task("log", function () {
    var message = "Something happened"; 
    gulp.src("").pipe(shell('echo "' + message + '" >> file.log'));
});

elixir(mix => {
    // Use the task in Elixir
    mix.task('log');

    // 绑定任务以在每次更改某些文件时运行
    mix.task('log', 'resources/somefiles/to/watch/**/*')
});
```

但是，如果我们需要更多的控制权，例如如果我们希望能够真正传递消息，这对于完成此特定任务非常重要，我们可以创建一个Elixir扩展：

```javascript
// Either in gulpfile.js, or in an external file and required in gulpfile.js
var gulp = require("gulp"),
    shell = require("gulp-shell"),
    elixir = require("laravel-elixir");

elixir.extend("log", function (message) {
    new Task('log', function() {
        return gulp.src('').pipe(shell('echo "' + message + '" >> file.log')); 
    })
    .watch('./resources/some/files/**/*');
});
```

和其他任何组件一样，我们并没有涵盖所有关于Elixir的知识，但希望你已经学会了足够的知识。 想要了解更多？ [查看官方文档](https://laravel.com/docs/mix)。

## 分页

对于Web应用程序中如此常见的内容，分页仍然可能非常复杂。 值得庆幸的是，Laravel有一个内置的 `pagination`，它也被默认挂在了Eloquent结果和路由器上，也就是我们可以在使用Eloquent的查询结果上和路由上使用分页。

### 基于数据库结果的分页

看到分页最常见的地方是当你显示数据库查询的结果并且单个页面的结果太多时。 Eloquent和查询构建器都从当前页面请求中读取页面查询参数，并使用它为任何结果集提供 `paginate()` 方法; 使用时你只需要传递每页显示多少个结果的数就可以了。

* 使用查询构造器分页 *

```php
// PostsController
public function index()
{
    return view('posts.index', ['posts' => DB::table('posts')->paginate(20)]);
}
```

这个例子定义了每页需要返回20篇文章，当在视图中显示i结果是，collection提供了一个 `links()` 方法(在Laravel5.1是 `render()`)，此方法可以输出分页控制同时带有 `bootstrap` 的样式。

* 在模板中渲染分页链接 *

```php
// posts/index.blade.php
<table>
@foreach ($posts as $post)
    <tr><td>{{ $post->title }}</td></tr>
@endforeach
</table>

{{ $posts->links() }}

// 默认, $posts->links() 输出的结果大概是这样：
<ul class="pagination">
    <li class="disabled"><span>&laquo;</span></li>
    <li class="active"><span>1</span></li>
    <li><a href="http://myapp.com/posts?page=2">2</a></li>
    <li><a href="http://myapp.com/posts?page=3">3</a></li>
    <li><a href="http://myapp.com/posts?page=2" rel="next">&raquo;</a></li>
</ul>
```

### 手动创建分页

如果你不想使用 `Eloquent` 或 查询构造器，或你想使用一个更复杂的查询，你可以自己创建满足自己的一个分页。幸运的是，Laravel 已经为我们提供了接口 `Illuminate\Pagination\Paginator` 或 `Illuminate\Pagination\lengthAwarePaginator` 。

这两个类不同的地方是 `Paginator` 只提供了上一页和下一页，而且也没有每页的链接；`LengthAwarePaginator` 需要知道结果总数，以便可以为每页生成一个链接。

`Paginator` 和 `LengthAwarePaginator` 都需要手动处理要传入视图中的数据。看下面的例子

```php
use Illuminate\Http\Request;
use Illuminate\Pagination\Paginator;

Route::get('people', function (Request $request) {
    $people = [...]; // huge list of people

    $perPage = 15;
    $offsetPages = $request->input('page', 1) - 1;

    // The Paginator will not slice your array for you
    $people = array_slice(
        $people,
        $offsetPages * $perPage,
        $perPage
    );

    return new Paginator( $people,$perPage);
});
```

> 注意，个版本之间会稍有差异，使用时注意查看官方文档。

## Message Bags

在Web应用程序中的另一个常见但痛苦的功能是在应用程序的各个组件之间传递消息，而最终目标是与用户共享它们。 例如，你的控制器可能希望发送验证消息："电子邮件字段必须是有效的电子邮件地址。" 但是，该特定消息不仅仅需要将其发送到视图图层; 它实际上需要经历一次重定向，然后结束于不同页面的视图层。 我们如何构建这种消息传递逻辑？

`Illuminate\Support\MessageBag` 是一个类，负责存储，分类和返回供最终用户使用的消息。 它通过key对所有消息进行分组，其中key可能是错误和消息之类的东西，并提供了获取所有存储的消息或仅使用特定key的方便方法，并以各种格式输出这些消息。

您可以像例子中那样手动启动一个MessageBag的新实例。

* 手动创建和使用 MessageBag *

```php
$messages = [
    'errors' => [
        'Something went wrong with edit 1!'
    ],
    'messages' => [
        'Edit 2 was successful.'
    ]
];

$messagebag = new \Illuminate\Support\MessageBag($messages);

// Check for errors; if there are any, decorate and echo
if ($messagebag->has('errors')) {
    echo '<ul id="errors">';
    foreach ($messagebag->get('errors', '<li><b>:message</b></li>') as $error) {
        echo $error;
    }
    echo '</ul>';
}
```

消息包还与Laravel的 `validator` 紧密相连：当验证程序返回错误时，它们实际返回一个 `MessageBag` 实例，然后你可以传递给你的视图或附加到重定向: `redirect('route')->withErrors($messagebag)`。

Laravel将MessageBag的一个空实例传递给每个视图，分配给变量 `$errors`，如果你在重定向中使用 `withErrors()` 刷新了一个消息包，它将被赋值给 `$errors` 变量。 这意味着每个视图总是可以假定它有一个 `$errors` MessageBag，它可以在它进行验证的任何地方检查它，下面的代码是我们基本可以放置在每个页面上的常见片段。

```php
// partials/errors.blade.php
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
        @foreach ($errors as $error)
            <li>{{ $error }}</li>
        @endforeach
        </ul>
    </div>
@endif
```

### 带命名的错误消息

有时候你需要区分消息包，不仅仅是按键（通知与错误），还有组件。 也许你在同一页面上有登录表单和注册表单; 你如何区分它们？
当使用 `withErrors()` 发送错误和重定向时，第二个参数就是包的名称：`redirect('dashboard')->withErrors($validator, 'login')`。 然后，在仪表板上，您可以使用 `$errors->login` 来调用之前看到的所有方法：`any()`，`count()`等。

## 字符串Helper、 多元化和本地化

作为开发人员，我们倾向于将大量文本视为大型占位符，等待客户将真实内容放入其中。 我们很少涉及这些块内的任何逻辑。
但在某些情况下，你会感激 Laravel 提供的字符串操作工具。

### 字符串Helper 和 多元化

Laravel拥有一系列操纵字符串的helper。 它们可以作为 `Str` 类的方法（例如 `Str::plural()`）使用，但大多数也有全局辅助函数（例如 `str_plural()`）。
Laravel文档详细介绍了所有的字符串helper，但这里有一些最常用的helper：
`e`: 是 `html_entities` 的快捷方式；
`starts_with, ends_with, str_contains`: 检查一个字符串（第一个参数）以查看它是以其开头还是以其结尾，还是包含其他字符串（第二个参数）
`str_is`: 检查字符串（第二个参数）是否匹配特定模式（第一个参数）, 例如，`foo*` 将匹配 `foobar` 和 `foobaz`
`str_plural(word,num), str_singular`: 对单词做复数化或单数化; 仅限英语（例如，`str_plural('god')` 返回 `dogs`）

### 本地化

本地化可让你定义多种语言并将任何字符串标记为翻译目标。 你可以设置备用语言，甚至可以处理复数变化。
在Laravel中，你需要在页面加载过程中的某个时刻设置应用程序语言环境，以便本地化帮助程序知道要从哪个翻译文件中提取。 你可以使用`App::setLocale($localeName)` 来做到这一点，你可能会把它放在服务提供者中。 现在你可以把它放在 `AppServiceProvider` 的`boot()`方法中，但是如果你最终得到的不仅仅是这一个语言环境相关的绑定，你可以创建一个 `LocaleServiceProvider`。

您可以在 `config/app.php` 中定义您的备用语言环境，您应该在其中找到一个 `back_locale` 键。 这使您可以为应用程序定义默认语言，如果Laravel找不到所需语言环境的翻译，将使用该语言。

#### 基本本地化

那么，我们如何调用翻译字符串呢？ 有一个帮助函数 `trans($key)`，它将为传递的key提取当前语言环境的字符串，如果它不存在，则从默认语言环境中获取它。 下面例子演示了基本翻译的工作原理。 我们将使用详细信息页面顶部的“返回仪表板”链接的示例。

* `trans()` 的基本使用 *

```php
// Normal PHP
<?php echo trans('navigation.back'); ?>

// Blade
{{ trans('navigation.back') }}

// Blade 指令(directive)
@lang('navigation.back')
```

假设我们现在正在使用 `en` 语言环境。 Laravel 将在 `resources/lang/en/navigation.php`文件中查找，它将返回一个数组。 back 将在该数组里查找，如果存在，它将返回其值。 看例子：

```php
// resources/lang/en/navigation.php
return [
    'back' => 'Volver al panel'
];

// routes/web.php
Route::get('/en/contacts/show/:id', function () {
// Setting it manually, for this example, instead of in a service provider App::setLocale('es');
    return view('contacts.show');
});

// resources/views/contacts/show.blade.php
<a href="/contacts">{{ trans('navigation.back') }}</a>
```

#### 本地化中的参数

前面的例子比较简单。 我们来挖掘一些更复杂的东西。 如果我们想要定义我们要返回哪个仪表板呢？ 看例子：

```php
// resources/lang/en/navigation.php
return [
    'back' => 'Back to :section dashboard'
];

// resources/views/contacts/show.blade.php
{{ trans('navigation.back', ['section' => 'contacts']) }}
```

正如你所看到的，用冒号（ `:section` ）前缀一个单词将其标记为可被替换的占位符。 `trans()` 的第二个可选参数是用于替换占位符的值数组。

#### 本地多元化

我们已经涵盖了多元化，所以现在想象一下，你正在定义自己的插件规则。 有两种方法可以做到这一点; 我们将从最简单的开始。

```php
// resources/lang/en/messages.php
return [
    'task-deletion' => 'You have deleted a task|You have successfully deleted tasks'
];

// resources/views/dashboard.blade.php
@if ($numTasksDeleted > 0)
    {{ trans_choice('messages.task-deletion', $numTasksDeleted) }}
@endif
```

我们有一个 `trans_choice()` 方法，它可以计算 item 的数量作为第二个参数; 并从中决定使用哪个字符串。 你也可以使用任何与`Symfony` 兼容的翻译定义更复杂的翻译部分; 例子如下。

```php
// resources/lang/es/messages.php
return [
    'task-deletion' => "{0} You didn't manage to delete any tasks.|" .
    "[1,4] You deleted a few tasks.|" .
    "[5,Inf] You deleted a whole ton of tasks."
];
```

## 单元测试

在本章中，我们主要关注 Laravel 的前端组件。 这些不太可能是单元测试的对象，但它们有时可能用于集成测试。

### 用Elixir进行单元测试

你不会为 Elixir 任务写任何测试。 但是，Elixir 提供了一些有助于你测试的功能，所以让我们来谈谈这些。
如果你将 `mix.phpunit()` 或 `mix.phpspec()` 添加到您的 `gulpfile.js` 中，那么您每次运行 `gulp` 时，它都会作为构建脚本的一部分一次内联地运行测试。
每次运行 `gulp watch` 时，Elixir 都会监听测试文件或任何其他核心文件（如 `routes/web.php`）的任何更改，并在每次对这些文件进行任何更改时重新运行 `PHPUnit` 或 `PHPSpec`。

### 测试Message和Error

有两种主要的测试消息和错误包的消息的方法。 首先，你可以在应用程序测试中执行一个行为，以设置最终显示在某处的消息，然后重定向到该页面并声明显示了相应的消息。
其次，对于错误（这是最常见的用例），可以使用 `$this->assertSessionHasErrors($bindings = [])` 断言会话存在错误。 看一下示例2，看看这可能是什么样子。

* 断言session中有错误 *

```php
public function test_missing_email_field_errors()
{
    $this->post('person/create', ['name' => 'Japheth']);
    $this->assertSessionHasErrors(['email']);
}
```

### 翻译和本地化

测试本地化的最简单方法是使用应用程序测试。 设置适当的条件（无论是通过URL还是会话），`visit()` 页面，并断言你看到了适当的内容。

## 总结

作为一个全栈的框架，Laravel为前端和后端提供了工具和组件。

Elixir是围绕常见Gulp构建任务的封装，使得使用最现代的构建步骤变得非常简单。 Elixir可以轻松添加CSS预处理器; JavaScript转换，拼接和缩小; 以及更多。

Laravel还提供针对前端的其他内部工具，包括分页，邮件和错误包以及本地化。