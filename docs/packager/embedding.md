---
slug: /packager/embedding
hide_table_of_contents: true
sidebar_label: Embedding
---

# 嵌入打包器

!!! info "注意了"
    这个页面是关于[TurboWarp 打包器](https://turbowarp.org/)的。如果你只想知道如何嵌入Scratch项目到网站，查看[另一个嵌入页面](/website/embedding/).


You can embed the output of the TurboWarp Packager into another website:

```html
<iframe src="path_to_project.html" width="480" height="360" allowtransparency="true" frameborder="0" scrolling="no" allowfullscreen></iframe>
```

Depending on the environment you used, where you stored the project, and what you named it, the `src` attribute will vary.

 - If you used "Plain HTML", it's just the path to the HTML file.
 - If you used "Zip", it's the path to the file `index.html` contained within the extracted zip.

If you have controls enabled, add 48 to the value of `height` to avoid the stage getting shrunk.

# Embedding the packager

:::info
This page is about the [TurboWarp Packager](https://turbowarp.org/). If you just want an easy way to embed a Scratch project into a website, see [the other embedding page](/embedding).
:::

You can embed the output of the TurboWarp Packager into another website:

```html
<iframe src="path_to_project.html" width="480" height="360" allowtransparency="true" frameborder="0" scrolling="no" allowfullscreen></iframe>
```

Depending on the environment you used, where you stored the project, and what you named it, the `src` attribute will vary.

 - If you used "Plain HTML", it's just the path to the HTML file.
 - If you used "Zip", it's the path to the file `index.html` contained within the extracted zip.

If you have controls enabled, add 48 to the value of `height` to avoid the stage getting shrunk.
