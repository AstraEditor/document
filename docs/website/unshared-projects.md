---
title: 私有作品不再能够在 TurboWarp 在线版中通过链接加载！
---

# 私有作品不再能够在 TurboWarp(或基于TurboWarp制作的Mod) 在线版中通过链接加载！

由于 Scratch API 被修改，作品如果未被发布(即未被共享)，则无法在 TurboWarp、forkphorus、Astra Editor 等第三方网站上打开。

此页面回答了人们大多数的问题，并列举了一些解决方法。请在与他人讨论这些变更之前完整阅读此内容，以免传播错误信息。

!!! warning "注意啦！"
    除了 scratch.mit.edu 之外，任何要求您输入 Scratch 密码的网站都是搞诈骗！即便它声称能让您与其他用户分享未公开的作品。这条警告没有任何例外，除非你不担心自己的账户和在线作品的安全。

## 发生了什么？ {#what-happened}

我们要明确指出：这些更改是由Scratch 团队做出的，而 TurboWarp 是一个与Scratch 团队无关的第三方网站，我们不能决定这些更改。

现在通过 Scratch API 获取作品需要一个“作品令牌”，而对于未共享的作品，该令牌仅能由作品的所有者(比如您)访问，即使您在这个浏览器中登录了您的 Scratch 账户，TurboWarp 也无法访问该令牌。这些令牌是临时生成的，过不了几分钟就失效了，因此所有者不能一次性提供一个可以永远生效的令牌。

未共享的作品通常是那些偶然能正常运行的作品，并非TurboWarp的预期主要用途。比如编译器和附加组件一直都是重点开发内容，并且在共享作品、从文件加载的作品以及桌面应用程序中都能正常运行。

## 解决方法 {#workarounds}

**测试您的个人作品：** 您可以在 Scratch 编辑器中使用“文件>保存至计算机”和“文件>从计算机加载”菜单选项将未共享的 Scratch 作品加载到 TurboWarp 中，或者将使用 TurboWarp 制作的作品上传到 Scratch。另外，许多人成功地使用 TurboWarp 进行作品开发，他们或是使用[网站](https://turbowarp.org)开发，或是使用[桌面应用程序](https://desktop.turbowarp.org/)开发，并且在作品完成后将其上传至 Scratch（请记得在操作时定期进行备份）。

**关于合作事宜：** 与他人分享作品的最佳方式就是直接在 Scratch 网站上进行分享。Scratch 是个非常友好的交流社区，而这就是 Scratch 希望你所做的事情。当然，未完成的作品也可以分享。Scratch 已经有 19 年的历史，而 TurboWarp 只有 6 年的历史。在没有 TurboWarp 的 13 年里，用户之间的合作进行得非常顺利，以后也会继续顺利进行下去。

**用于嵌入其他网站：** 若要将未共享的作品嵌入到其他网站中，您可以选择在 Scratch 上共享该作品，或者在 Scratch 编辑器中通过“文件 > 保存到计算机”菜单将作品下载到您的计算机上，然后使用 [TurboWarp 打包器](https://packager.turbowarp.org/) 将此作品转换为一个可独立[嵌入](../packager/embedding.md)的文件。

## 不过这是件好事 {#good-thing}

对于未共享作品的保护工作，Scratch 团队已经<s>(脏话)</s>拖更了十多年。

不要觉得没有人遭遇过作品被窃取，他们只是不知道那些未共享的作品实际上不是私有的，哪怕 Scratch 给您写了个大大的“只有您才能看到它”。许多未共享的作品都包含着孩子和他们的朋友、他们的家人甚至其他个人信息的素材，而人们却以为这些未共享的作品是真正私密且安全的。

在大多数其他的大型网站中，那些本来应该是私密的却成为人人都能查看的内容的情况，都会被视为严重的安全漏洞，并通常会给发现者提供高额赏金。比如，[YouTube 曾向一位安全研究人员支付了 5000 美元](https://bugs.xdavidhu.me/google/2021/01/11/stealing-your-private-videos-one-frame-at-a-time/)，感谢他报告了一个 YouTube 的漏洞，因为漏洞让他们可以查看任何私密视频的低分辨率图像。

我们一直以来的立场，就是让各个 Scratcher 知道自己的未共享作品并没有真正实现私密化，并且应该与 Scratch 团队沟通。估计是有足够多的人这样做了，Scratch 团队才知道去修这个BUG。

令人印象深刻的是，尽管这种行为造成了诸多隐私方面的违规行为，但 Scratch 却并未因此而遭受重创而被起诉。<small>(原句在TurboWarp文档被隐藏)</small>

## 开发者注意事项 {#developers}

此部分是为开发与 Scratch 相关的第三方工具的人准备的。

新的作品下载方式是：首先从 `https://api.scratch.mit.edu/projects/[ID]` 获取 `project_token` 字段，然后利用该字段获取URL `https://projects.scratch.mit.edu/[ID]?token=TOKEN`

如果您正在使用 JavaScript，我们提供了一段示例代码供您参考，这些代码可在浏览器中运行。如果您的代码运行在服务器端（例如 Node.js），则应将 `https://trampoline.turbowarp.org/api/projects/` 替换为 `https://api.scratch.mit.edu/projects/`，因为服务器不受 [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 的限制。我们无法保证 trampoline.turbowarp.org 的运行稳定性，请自行承担使用风险。您可能还会对 [sb-downloader](https://github.com/forkphorus/sb-downloader)（包含一些简单的 API）感兴趣，它是一个完整的作品下载器，可以实现完整的下载功能。

```js
const getProjectMetadata = async (projectId) => {
    // 如果直接在浏览器运行，您需要借助像 trampoline.turbowarp.org 这样的服务来访问 Scratch API
    // 如果代码跑在 Node.js 上，您就可以直接使用 https://api.scratch.mit.edu/projects/${projectId} 进行访问
    const response = await fetch(`https://trampoline.turbowarp.org/api/projects/${projectId}`);
    if (response.status === 404) {
        throw new Error('The project is unshared or does not exist');
    }
    if (!response.ok) {
        throw new Error(`HTTP error ${response.status} fetching project metadata`);
    }
    const json = await response.json();
    return json;
};

const getProjectData = async (projectId) => {
    const metadata = await getProjectMetadata(projectId);
    const token = metadata.project_token;
    const response = await fetch(`https://projects.scratch.mit.edu/${projectId}?token=${token}`);
    if (!response.ok) {
        throw new Error(`HTTP error ${response.status} fetching project data`);
    }
    const data = await response.arrayBuffer();
    return data;
};

getProjectData('60917032').then((data) => {
    console.log(data);
}).catch((error) => {
    console.error(error);
});
```

我们在此将这段代码段置于[无许可协议](https://unlicense.org/)之下进行发布。
