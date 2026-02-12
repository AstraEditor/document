---
title: 新编译器
---

# 亲斤编译器

**编译器**是 TurboWarp 中的一个内置组件，负责将作品转换为 JavaScript 代码。在 2025 年 9 月 20 日，我们推出了一个全新的编译器，它在分析作品内部的脚本并生成更快速的 JavaScript 代码方面表现相较于之前的版本更出色。

这是我们迄今为止最大的一次改动。我们已经尽力进行了全面测试，但**仍存在一些小问题**。您若遇到作品故障，请在[常规反馈渠道](https://scratch.mit.edu/users/GarboMuffin/#comments)中报告。如果您的作品出现故障，您可以先使用 https://experiments.turbowarp.org/old-compiler/ 链接中的内嵌旧编译器的编辑器，直到我们修复新编译器中的错误为止。

!!! warning "注意啦"
    新的编译器破坏了部分自定义扩展功能。有关详细信息和解决方法，请参阅下面的[扩展](#extensions)部分。

新的编译器已经应用于在线版、打包工具以及桌面应用程序（1.15.0 及更高版本）。

## 性能比较 {#performance}

性能提升的情况因作品而异。有些作品运行速度提高了两倍，而有些作品则没有变化，但不应该有任何作品的运行速度变慢。

|测试作品|旧编译器|新编译器|
|:-:|:-:|:-:|
|[Linux in Scratch](https://turbowarp.org/1201938491)<br>解码时长<br>越短越好|21秒|12秒|
|[Faster SHA-256 Hash](https://turbowarp.org/611788242)<br>哈希频率<br>越高越好|2711 次/秒|3010 次/秒|
|[Quicksort](https://turbowarp.org/310372816)<br>排序 200000 个随机项<br>越短越好|0.0515 秒|0.0451 秒|

（使用 Chromium 140、Arch Linux、i7 4790k 处理器并关闭循环计时器时进行测试）

## 限制 {#limitations}

### 循环计时器 {#warp-timer}

[循环计时器](settings/warp-timer.md)会强制那些设置为“运行时不刷新屏幕”的自定义块在运行 500 毫秒后短暂暂停，以防止无限循环导致您丢失未保存的工作内容。这就是为什么当您打开 TurboWarp 编辑器时，循环计时器会自动启用的原因。

不幸的是，循环计时器破坏了新编译器用于优化作品的诸多假设，因此在编辑器中使用或开启循环计时器时，您不会看到您所期望的显著的性能提升。值得一提的是，它们的运行速度并不会比在旧编译器中更低。

### 扩展兼容性 {#extensions}

TurboWarp 扩展列表中包含的所有扩展都将保持原有功能不变，而且绝大多数自定义扩展程序也将保持原有功能不变。

部分自定义扩展使用了一个名为`i_will_not_ask_for_help_when_these_break`的 API 来更直接地与编译器集成。我们给这个 API 取了这么一个奇怪的名字，是因为我们知道它可能会在某个时候坏掉，而且我们不希望因为一小部分扩展而限制我们无法在需要时修改编译器的内部结构。如果您的作品依赖这些扩展，您可以前往 https://experiments.turbowarp.org/old-compiler/ 使用旧的编译器，直到扩展能够与新编译器兼容为止。

如果您还需要对作品进行打包，您可以前往 https://packager-legacy.turbowarp.org/old-compiler/ 。

## 技术概述 {#technical-overview}

其基本原理是，编译器会分析脚本，以确定每个变量在脚本中的每个点上可能具有何种值。这使得它能够消除不必要的类型转换，并生成更专一的代码。旧版的编译器也曾试图这样做，但无法对循环或条件语句（即含有分支的语句）进行分析。

现在请查看下列代码以下这段代码，它会将 1 到 100 之间的整数相加。假设该代码块被设置为“运行时不刷新屏幕”且循环计时器已关闭。

![Script that sums 1 to 100](./assets/sum-1-to-100.svg)

旧的编译器将会生成这样的代码：

```js
sum.value = 0;
i.value = 1;
for (var a0 = 100; a0 >= 0.5; a0--) {
    sum.value = ((+sum.value || 0) + (+i.value || 0));
    i.value = ((+i.value || 0) + 1);
}
```

`(+something.value || 0)` 是编译器将变量（在不是数字类型的情况下）转换为数字的方式。在这种情况下，这显然是多余的，因为这些变量本来就一直都是数字类型的，但旧的编译器并未察觉到这一点。

然而，新的编译器会这样生成代码：

```js
sum.value = 0;
i.value = 1;
for (var a0 = 100; a0 > 0; a0--) {
    sum.value = (sum.value + i.value);
    i.value = (i.value + 1);
}
```

铛铛！这样的代码不再存在不必要的类型转换，现在的编译器甚至还能发现迭代次数始终是整数，因此无需处理小数形式的迭代次数这一特殊情况。不积跬步无以至千里，像这样的小改动累积起来会产生重大影响。

如果启用了循环计时器或者代码块未设置为“运行时不刷新屏幕”，新编译器生成的代码与旧编译器生成的代码相同。这是因为重复块可能会在完成所有迭代之前暂停，因此其他脚本有机会以编译器不知道的方式更改变量（这真的太坏了喵）。例如，如果另一个脚本在循环执行的时候突然将“i”变量设置为字符串，那么 `i.value + 1` 将会像“连接”块一样起作用，而不是进行加法运算。在这种情况下，类型转换就成为必要，以确保脚本始终能正确运行。

（我们手动对这个页面的 JavaScript 代码进行了整理，实际生成的代码没有格式，也没有有意义的对象名）

## 鸣谢 {#credits}

[Tacodiva](https://scratch.mit.edu/users/Tacodiva7729/)几乎独自完成了所有工作，并在极其漫长的审核过程中坚持不懈地努力着。

早期的测试人员在产品发布前帮助我们发现并修复了许多错误：

 * [Krypto](https://scratch.mit.edu/users/KryptoScratcher/)
 * [Vadik1](https://scratch.mit.edu/users/Vadik1/)
 * [SpinningCube](https://scratch.mit.edu/users/SpinningCube/)
 * [GamingWithDominic](https://scratch.mit.edu/users/GamingWithDominic/)
 * [Scratch_Fakemon](https://scratch.mit.edu/users/Scratch_Fakemon/)
 * [LSPECTRONIZTAR](https://scratch.mit.edu/users/LSPECTRONIZTAR/)
 * [Terminal](https://scratch.mit.edu/users/windowscj/)
