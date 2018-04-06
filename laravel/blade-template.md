
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

### 条件

### 循环

### or

## 模板继承

### 定义section

### @parent

### @inclde

### @each

## 视图设计和注入

### 绑定数据到视图使用 View Composers 

### Blade服务注入

## 自定义Blade directives

### 自定义 directives 的参数

### 示例：使用自定义 directives

## 总结