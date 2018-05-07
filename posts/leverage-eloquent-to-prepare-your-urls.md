# 在Eloquent中封装URL属性来统一管理视图中的路由

在 Laravel 应用程序中拥有数十个甚至数百个视图并不罕见。很快就会对视图中使用的路由管理失控。可以想象如果你在视图中做了多少这样的事情。

```html
<a href="{{ route('users.show', ['id' => $user->id]) }}">{{ $user->name }}</a>
```

如果想对路由名称或者默认查询参数值进行修改，就需要在整个应用中进行大量的字符串替换操作，但是这会带来重复性的工作量，同时也可能会漏掉一些地方。

那我们可以做些什么来更好的处理这个问题呢？下面有两种实现方式。

## 第一种： 修改Eloquent

```php
// app/Models/User.php

<?php

namespace App\Models;

class User {

  protected $appends = [
    'url'
  ];

  public function getUrlAttribute()
  {
    return route('users.show', $this);
  }
}
```

然后在视图中就可以这样使用

```html
<a href="{{ $user->url }}">{{ $user->name }}</a>
```

感觉写的很干净，是吧？但对于高级开发人员，你可能希望采用下一种方法。

## 第二种. 在Eloquent中使用URL Presenter

咋一看你可能会觉的比较眼熟，是的，这里我们定义一个url属性，但与一般写法不同的是是我们会返回一个 `presenter` 实例。

```php
// app/Models/User.php

<?php

namespace App\Models;

use App\Presenters\User\UrlPresenter;

class User {

  protected $appends = [
    'url'
  ];

  public function getUrlAttribute()
  {
    return new UrlPresenter($this);
  }
}
```

```php
// app/Presenters/User/UrlPresenter.php

<?php

namespace App\Presenters\User;

use App\User;

class UrlPresenter {

    protected $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function __get($key)
    {
        if(method_exists($this, $key))
        {
            return $this->$key();
        }

        return $this->$key;
    }

    public function delete()
    {
        return route('users.delete', $this->user);
    }

    public function edit()
    {
        return route('users.edit', $this->user);
    }

    public function show()
    {
        return route('users.show', $this->user);
    }

    public function update()
    {
        return route('users.update', $this->user);
    }
}
```

然后就可以这样使用

```html
<a href="{{ $user->url->show }}">{{ $user->name }}</a>
```

如上，现在的视图并不关心我们如何确定URL，只是返回一个URL即可。 这样做的好处在于，你修改视图中的任何路由，只需编辑两个文件，而不是数百个。

> 原文地址： [https://laravel-news.com/leverage-eloquent-to-prepare-your-urls](https://laravel-news.com/leverage-eloquent-to-prepare-your-urls)