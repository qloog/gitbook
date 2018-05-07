# Laravel 5.6 删除了Artisan Optimize命令

`php artisan optimize` 命令在 Laravel 5.5中已经被弃用了。 现在，官方发布了 Laravel 5.6之后已经被移除了。

在Laravel 5.5中，在 `post-install-cmd` 和 `post-update-cmd` 中使用 `optimize` 不在有效了。 现在 用 Laravel 5.6，你不需要使用 `optimize` 了。

依据 Laravel 5.6 升级指导：
> 先前弃用的 `optimize` Artisan命令已被删除。随着包括OPcache在内的PHP本身的最新改进，`optimize` 命令不再提供任何相关的性能优势。

在 Laravel 5.5 中使用 `optimize` 命令会生成一个弃用的警告。现在发布 Laravel5.6之后就完全移除了。 所以如果你是正在升级到 Laravel 5.6，确保从 `composer.json` 文件中移除这个命令。

> 原文地址： [https://tutsforweb.com/laravel-5-6-removed-artisan-optimize/](https://tutsforweb.com/laravel-5-6-removed-artisan-optimize/)