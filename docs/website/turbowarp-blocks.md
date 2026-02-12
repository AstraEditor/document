---
title: TurboWarp 积木
---

# TurboWarp 积木

TurboWarp 设计了几个积木，使您能够使用此前在 Scratch 作品中无法实现的一些功能。

更新：TurboWarp 现已支持非沙盒运行的扩展，这些扩展能够添加新的块！https://extensions.turbowarp.org/

## "启用编译？"和"在TurboWarp？" {#is-compiled}

![启用编译？](./assets/is-compiled.svg)

查看 https://scratch.mit.edu/projects/414716080/

这些积木与 Scratch 是“兼容”的，因为它们实际上只是被修改过的参数返回值。

!!! warning "注意啦！"
    从这条警告之后的每一个块都与 Scratch **不兼容**。使用这些块创建的作品无法上传至 Scratch 网站。如果您不使用任何 TurboWarp 特有的块，那么在 TurboWarp 中制作作品并将其上传至 Scratch 将不会出现任何问题。

## 最后按下的按键 {#last-key-pressed}

![last key pressed](./assets/last-key-pressed.png)

它会告诉您上一次按下的是哪个按键，其使用方法大致如下：

![when any key pressed, do something with last key pressed](./assets/how-to-use-last-key-pressed.png)

## 鼠标按下？ {#mouse-button-down}

![primary mouse button down?](./assets/mouse-button-down.png)

它很像“鼠标按下？”，但允许您选择侦测哪个按钮。请记住，由于 Scratch 对鼠标输入的解释方式，像“鼠标左键是否按下？”这样的代码块可能会显示为“真”，而标准的“鼠标按下？”则可能显示为“假”。

* （0）主按钮通常为左键点击
* （1）中间按钮通常为滚轮
* （2）次按钮通常为右键点击（执行过该块后将禁用舞台的右键点击功能，这通常在在线版中出现）