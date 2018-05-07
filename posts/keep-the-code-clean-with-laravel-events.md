# 使用Laravel事件保持代码清洁

在许多情况下，当我们的应用程序变得更大，并且需要处理越来越多的任务时，我们的控制器，演示者或服务的大小可以快速增加，并且很容易变得难以处理，无法测试和缓慢。

## 单一职责

重新组织我们的逻辑，并将不同类型的任务放到专门的类中，可能会非常有用，并且这是推荐的“最佳”实践。

为什么？处理更多但更小的碎片比处理巨大巨石中的所有物件要容易得多。最重要的是，你的应用程序变得更加可测试，你不会破坏SRP，并且最终你可以轻松获得更干净更漂亮的代码。

## 开始使用事件

其中一项有用的技巧是Laravel的 Event 服务。 首先，在各种情况下，通过将任务移至事件类，可彻底清理控制器。 使用事件的另一个原因是队列系统，它允许你的应用程序异步完成作业，这可以极大地加速你的应用程序。

事件系统有两个重要部分。 事件本身和侦听器(Listener)可以绑定到特定的事件。 事件类负责序列化传入数据，并将其传递给侦听器，旨在在事件触发后处理逻辑的内容。

## 实际中使用事件 (Event)

触发事件从未如此简单，我们所要做的就是调用 `event()` helper，并将事件的类作为参数。如: `event(UserRegistered::class)` 。

通过一个常见的例子，让我们看看它在现实生活中的作用。在我们的应用程序中，我们希望向我们的新用户发送欢迎电子邮件。

首先，我们需要在 `EventServiceProvider` 中绑定事件和侦听器。如果我们想要处理特定事件的不同逻辑，我们可以将更多监听器附加到事件上。

```php
// app/Providers/EventServiceProvider.php

<?php

namespace App\Providers;

use Illuminate\Support\Facades\Event;
use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        'App\Events\UserRegistered' => [
            'App\Listeners\SendWelcomeEmail',
        ],
    ];
}
```

当绑定准备就绪时，我们可以使用 `php artisan event:generate` 生成一对 事件-侦听器，或者我们可以使用 artisan make 命令单独创建它们。

我们的事件没有其他责任，只是将用户指定为公共财产，可能会传递给侦听器。

```php
// app/Events/UserRegistered.php

<?php

namespace App\Events;

use App\User;

class UserRegistered
{
    public $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }
}
```

我们的侦听器Listener是重要的一部分。 它实现了 `ShouldQueue` 接口，这意味着这个事件将被推送到队列堆栈。 在 `handle` 方法中，我们正在实现将电子邮件发送给给定用户的逻辑。

```php
// app/Listeners/SendWelcomeEmail.php

<?php

namespace App\Listeners;

use App\Mail\WelcomeEmail;
use App\Events\User\Created;
use Illuminate\Support\Facades\Mail;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;

class SendWelcomeEmail implements ShouldQueue
{
    use InteractsWithQueue;

    public function handle(Created $event)
    {
         Mail::to($event->user)->send(new WelcomeEmail($event->user));
    }
}
```

重组我们的逻辑可能非常有用，尤其是当你需要处理大量任务时。事件和侦听器可能对你有很大的帮助。你可以在这里找到官方的[Laravel事件文档](https://laravel.com/docs/5.6/events)。

> 原文地址： [https://pineco.de/keep-the-code-clean-with-laravel-events/](https://pineco.de/keep-the-code-clean-with-laravel-events/)