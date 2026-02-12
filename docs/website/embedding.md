---
title: 嵌入
---

# 嵌入

您可以借助 TurboWarp 将 Scratch 作品嵌入到您的网站中，方法是使用 `<iframe>` 标签。为了获得最佳体验，可以使用以下模板：

```html
<iframe src="https://turbowarp.org/414716080/embed" width="482" height="412" allowtransparency="true" frameborder="0" scrolling="no" allowfullscreen="" style="color-scheme: auto"></iframe>
```

您可以对这些属性进行需要的调整：

 - `src="https://turbowarp.org/414716080/embed"` 中包含了您要嵌入的作品的 ID。您需要对其进行更改。这里的作品 ID 便是 414716080，但您可以将该数字替换为其他作品的 ID。您还可以按照以下列出的方式添加其他 URL 参数。
 - `width="482" height="412"` 用于定义嵌入舞台的尺寸。播放器会自动根据您在此处指定的尺寸进行调整，因此您可以更改这些数字。舞台周围的边框宽高均为 2 个像素，而上方的控制栏则额外占用 50 个像素的高度。因此，要显示为 480x360 的舞台尺寸，应使用 482 和 412。
 - 另外，您还可以在属性中添加 `loading="lazy"` ，这能让浏览器在用户滚动到该 iframe 附近时再进行加载（这就是懒加载）。如果嵌入内容位于页面底部较远的位置且无需立即加载，这样做可以提高性能，提升网页加载速度。

其余的属性可以不用更改。下面就是它们的功能说明，如果您想知道的话：

 - `allowtransparency='true'` 选项使得嵌入的界面可以拥有透明背景，因此嵌入界面后面的任何颜色或图像仍能清晰可见。
 - `frameborder="0"` 去除 iFrame 四周那个丑陋的边框。
 - `scrolling="no"` 确保 iframe 中不会出现任何不该出现的滚动条。
 - `allowfullscreen=""` 保证全屏按钮能够正常工作。如果删除了此属性，或者你的设备太垃圾，不支持全屏模式，那么该按钮将不会显示出来。
 - `style="color-scheme: auto"` 确保当您的页面采用暗黑模式时，嵌入元素的透明背景能够正常显示。此属性无法让您控制嵌入内容内部的显示效果，它只是固定了透明度。

以下是该示例嵌入的实际效果展示：

<iframe src="https://turbowarp.org/414716080/embed" width="482" height="412" allowtransparency="true" frameborder="0" scrolling="no" allowfullscreen="" style={{colorScheme: "auto"}}></iframe>

## 未共享的作品无法嵌入 {#unshared-projects}

[未共享的作品](unshared-projects.md)无法在嵌入中显示。请确保您要嵌入的作品是分享出去的，或者使用[TurboWarp 打包器](https://packager.turbowarp.org/)替代。

## URL 参数 {#url-parameters}

所有[标准 URL 参数](url-parameters.md)仍然可用。您可以利用这些参数来设置用户名及其他内容。

此外，这里有一些仅在嵌入模式下才可用的特殊参数：

### 自动播放 {#autoplay}

嵌入功能支持`autoplay`参数，当作品加载时，该参数会自动运行作品。例如：https://turbowarp.org/15832807/embed?autoplay

请注意，声音块可能在用户与作品进行交互（例如点击操作）之前无法正常工作。这是浏览器本身的限制。而 TurboWarp 无法解决这一问题，毕竟你不能让儿子打老子。

### 设置按钮 {#settings-button}

您还可以通过使用`settings-butto`”参数在嵌入内容中显示一个设置按钮，这样就能打开一个与编辑器中“高级设置”菜单类似的界面。例如： https://turbowarp.org/15832807/embed?autoplay&settings-button

### 全屏背景色 {#fullscreen-background}

在没有开启全屏模式时，嵌入部分是透明的，因此如果您想更改父元素的背景颜色，可以对其进行样式设置。

在全屏模式下，嵌入界面会根据用户电脑是否开启暗黑模式而采用白色或近似黑色的背景色。

若要修改这个设置，请将`fullscreen-background`参数设置为一个 CSS 颜色值，例如`black`或`rgb(50，90，100)`，例如： https://turbowarp.org/15832807/embed?fullscreen-background=yellow

您还可以使用十六进制颜色代码，只需用百分号编码将`#`符号进行转义即可：`%23abc123`。

### 插件 {#addons}

By default, embeds have no addons enabled. This can be overridden with the `addons` parameter, which is a comma separated list of addon IDs to enable. For example:
默认情况下，嵌入组件未启用任何附加插件。不过可以通过使用`addons`参数来更改这一设置，该参数是一个以逗号分隔的插件 ID 列表，用于启用相应的插件。例如： https://turbowarp.org/15832807/embed?addons=pause,gamepad,mute-project

有用的插件和它们的 ID：

 - `pause`是"暂停按钮" 插件
 - `mute-project`是"播放器禁音"插件
 - `remove-curved-stage-border`是"移除弯曲的舞台边框"插件
 - `drag-drop`是"文件拖拽"插件
 - `gamepad`是"游戏手柄支持"插件
 - `editor-buttons-reverse-order`是"项目控件反向顺序"插件
 - `clones`是"克隆计数器"插件

（使用 AstraEditor 可以查看所有插件的 ID）

其他插件对嵌入内容不会产生任何影响。

## 安全注意事项 {#security}

如果您使用用户提供的信息来生成嵌入链接，那么您需要对所有参数进行整理，以确保用户没有输入错误的 URL 参数，因为某些参数可能会导致以外发生。

## 需要更多设置？ {#packager}

使用[TurboWarp 打包器](https://packager.turbowarp.org/)可以更准确地控制加载界面、强调色、控制选项等。您还可以非常轻松地[嵌入打包后的文件](/packager/embedding.md){.data-perviwe}。

## 协议 {#license}

TurboWarp 的许可协议遵循 [GPLv3.0](https://github.com/TurboWarp/scratch-gui/blob/develop/LICENSE)。我们认为，一个基于 GPLv3.0 许可协议的作品的 `<iframe>` 并不会构成基于 GPLv3.0 的衍生作品，而是会形成一种“组合作品”，其并不需要遵循与衍生作品相同的条件。然而，我们不是专业的律师，开源协议也不是法律建议。如果您对此有疑问，请咨询律师，反正你不遵守协议我们也不能把你怎么样，最多挂到耻辱柱上。