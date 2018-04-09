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

#### 版本

#### 测试

#### Elixir扩展

## 分页

### 基于数据库结果的分页

### 手动创建分页

## Message Bags

### 命名错误消息

## 字符串Helper、 多元化和本地化

### 字符串Helper 和 多元化

### 本地化

#### 基本本地化

#### 带有参数的本地化

#### 本地多元化

## 单元测试

### 用Elixir进行单元测试

### 测试Message和Error

### 翻译和本地化



## 总结

作为一个全栈的框架，Laravel为前端和后端提供了工具和组件。

Elixir是围绕常见Gulp构建任务的封装，使得使用最现代的构建步骤变得非常简单。 Elixir可以轻松添加CSS预处理器; JavaScript转换，拼接和缩小; 以及更多。

Laravel还提供针对前端的其他内部工具，包括分页，邮件和错误包以及本地化。