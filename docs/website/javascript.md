---
title: 怎么下载编译后的JS代码?
---

# 怎么下载编译后的JS代码?

## 简单来说 {#short}

 * 如果你只是想要打包作品为 HTML 或者可执行程序，请访问 https://packager.turbowarp.org/ 进行打包；
 * 如果你想将 Scratch 作品转换为给人读和编写的 JavaScript 文件，使用 https://leopardjs.com/ 进行转换，而不是 TurboWarp。

## 具体来说 {#long}

TurboWarp 生成的代码不是给人看的，更别说编辑了。把这个当学习材料只会让你变得人机，这是为了提高兼容性和运行速度的必要行为。

例如，在常规的 JavaScript 中，访问列表项非常简单，只需要 `myList[myIndex]`。但 TurboWarp 则可以根据列表块周围的上下文执行各种抽象操作，从 `(b1.value[(b0.value | 0) - 1] ?? "")` 到 `listGet(b0， b1.value)` 都有可能。当然，`b0` 和 `b1` 是 TurboWarp 会使用的实际变量名，而 `listGet` 至少是 TurboWarp 运行时的函数，而非 JavaScript 标准方式。该代码也没有任何格式。还有更多代码示例可在[另一个页面](how.md)中查看。

如果您想将 Scratch 作品转换为可读且可编辑的 JavaScript 代码，请访问 https://leopardjs.com/ 进行操作。

??? warning "如果你真的知道你在干啥……"    

    在运行你的作品之前在 JavaScript 控制台中执行以下代码：

    ```js
    vm.enableDebug();
    ```

    然后，当 JavaScript 编译完成后将会被输出到控制台。

    如果你不清楚“JavaScript 控制台”是什么，也不知道如何访问它，那你最好别去看生成的 JavaScript 代码。
