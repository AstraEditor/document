---
title: 云变量
---

# 云变量

TurboWarp 有独立于 Scratch 的专属云变量服务器。

以下是一些需要牢记的事项：

- 每个人都可以通过“编辑 > 更改用户名”菜单来更改自己的用户名。名为“griffpatch”的用户很可能并非真正的 griffpatch。
- 为减少滥用情况，云变量服务器会拒绝任何不属于现有 Scratch 账户的用户名。
- 由于可能存在滥用风险，Scratch 团队成员的名字不能被使用，包括 ScratchCat。
- 变量长度限制已增加到 100000 个字符。
- 云变量仍然只能存储数字。
- 云变量会在服务器重启或作品中无人操作较短时间后被重置，因此排行榜一类的内容不会保存很长时间。
- 当编辑器打开时，云变量会被禁用。
- 不要滥用云变量来创建未经审核的聊天室。
- 没有公开的云变量历史记录。
- 使用视频侦测扩展时，侦测颜色或运动的功能会被禁用。

---

## 适用于机器人开发者及高级用户 {#advanced}

我们允许并鼓励开发机器人和定制客户端。然而，由于存在持续的滥用行为，我们要求您遵守以下几条规则。请记住，**这是由志愿者运营的免费服务**。解析消息所需的 CPU 资源以及向其他用户发送消息所需的带宽并非免费的。以下内容适用于所有用户以及云变量库的作者。

### 协议 {#protocol}


该协议与 [Scratch 的云变量协议](https://github.com/TurboWarp/cloud-server/blob/master/doc/protocol.md)完全相同。我们在 Node.js 提供了一个[基础参考库](https://www.npmjs.com/package/@turbowarp/mist)。由于该协议是完全开放的，如果您不想使用我们的库，我们也不强求。

### 必须提供用户代理 {#user-agent}

机器人在连接时必须提供有效的[用户代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)标头。这包括联系信息（比如 Scratch 个人资料链接、电子邮件地址、GitHub 问题页面等）以及正在使用的云变量库的名称和版本（如果适用）。具体的语法并不重要，只要是给人看的就行。就像这样：
``` txt
multiplayer leaderboard bot by https://scratch.mit.edu/users/TestMuffin
cloud-variable-library/1.0.1 contact@example.com
```

唯一的例外是，如果您的机器人运行在浏览器环境中，您便无法控制用户代理信息。在这种情况下，您的浏览器会自动包含其他诸如“Origin”这样的标头，并附上您网站的名称。伪装成浏览器是不被允许的，而且很容易被检测出来。

请咨询您所使用的云变量库的作者，或者查阅您的 WebSocket 库的文档，了解有关自定义标头的设置方法，以了解如何添加“用户代理”信息。

??? info "如果你正在开发云变量库"
    您需要提供一个用于设置用户代理的 API，并且强制使用该 API。例如，对于某个假设的云变量 API，您可能会有这样一个选项：

    ```js
    const CloudConnection = require('...');

    const connection = new CloudConnection({
        username: '...',
        projectId: '...',
        // 明显的开始内容
        // 上传此内容！
        contactInformation: 'contact@example.com'
        // 明显的结束内容
    });

    connection.on('connected', () => { /* ... */ });
    connection.on('set', (name, value) => { /* ... */ });
    ```
    
    您的库将会看到“contactInformation”选项，并将其与您的库的名称和版本进行组合，最终生成一个类似于“CloudConnectionLib/0.3.3 contact@example.com”的用户代理字符串。

    如果有人没有填写“contactInformation”，便阻止它继续操作，而您将会收到用户发出的毫无细节的荒谬错误报告，比如“云变量无法连接”，这可真“妈妈的”。所以，不如给他们一个友好的错误提示，这样他们就能自己解决问题而无需打扰您了。

    要实际设置用户代理信息，请查看您所使用的 WebSocket 库的相关文档。这些文档可能不会特别提及“用户代理”这一项，但通常会提到如何设置通用的标头。例如，使用 Node.js 的 [ws](https://www.npmjs.com/package/ws) 客户端，您只需要这样做：

    ```js
    const ws = new WebSocket("wss://clouddata.turbowarp.org", {
      headers: {
        "user-agent": userAgentGoesHere
      }
    });
    ```

### 作品 ID {#project-id}

作品 ID 并不局限于数字——它可以是您想要的任何文本。如果您为不在 Scratch 网站上运行的作品使用自定义编号，请使用类似“example.com/my-project”的文本，方便我们验证您的作品是否合法。如果我们在一个作品 ID 下看到大量不正常的云活动，我们将禁用该作品 ID。

??? info "如果您正在开发云变量库"
    由于此原因，您的作品 ID 选项应设为字符串，而不是整数或其他数值类型。

### 用户名 {#username}

云变量协议要求您提供一个用户名。服务器会尽力在允许连接之前确保所有用户名都是安全的。我们建议您将用户名设置为“player”后跟 2 到 7 个随机数字，这样您的连接会更快启动（我们不会要求 Scratch API 进行验证）。如果您的机器人需要特定的用户名，请将其存储在单独的变量中。

??? info "如果您正在开发云变量库"
    如果您强制用户填写有效的用户代理信息，那么用户名就变得多余了，所以您可以省去用户名这一选项，而是自动生成一个随机的用户名。

### 不要频繁地重连 {#one-connection}

我们发现了一种模式：机器人会不断开启连接、关闭连接，然后立即再次开启新的连接，如此循环往复。最终的结果是，这个机器人运行速度极慢，它所消耗的网络和 CPU 资源远远超出了其应有的必要量，这是不被允许的。我们认为这是因为一些设计欠佳的云变量库提供的 API 让人们这样编写代码：

```py
while True:
    value = cloudlibrary.get_var(project_id, username, user_agent, variable_name)
    print(f"{variable_name} is {value}")
```

在 `cloudlibrary.get_var` 的实现中，是通过先建立连接然后立即关闭连接来完成的。然而，库应当提供[事件驱动](https://en.wikipedia.org/wiki/Event-driven_programming)的 API，不需要持续向服务器请求最新的值，而只打开一个 WebSocket，让服务器在发生数据更新时发送更新信息。因为 WebSocket 的效率非常高：如果没有变量发生变化，连接就会保持空闲状态。如果有很多变量在变化，您将能够尽快收到更新信息。等效的代码可能如下所示：

```py
def on_set(name, value):
    print(f"{name} is {value}")
connection = cloudlibrary.connect(project_id, username, user_agent, on_set)
```

只要实现方式是基于事件驱动的，并且内部使用一个连接，就可以提供一个类似于 `get_var` 的 API（那么 `get_var` 就会返回最近接收到的值）。这只需要做一丢丢的修改。

### 缓存更新内容 {#buffering}

为了提高性能，服务器会将多个云变量的更新内容先进行缓冲，然后再一次性发送出去。这些更新内容并不保证按照接收的顺序依次发送，而且某些更新内容可能会被完全跳过。由于这种缓冲机制的存在，每秒发送超过 10 次更新变量的操作完全是多余的。

### 回应激活(pings)信号 {#pings}

服务器会定期发送一个[WebSocket 激活帧](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#pings_and_pongs_the_heartbeat_of_websockets)，您必须回应一个“pong”，否则连接将会断开。请参考您所使用的 WebSocket 库的文档，了解如何启用“ping/pong”支持（如果默认情况下未启用的话）。

### 重要的调试信息 {#debug}

为了让我们的工作更简便，您以及任何使用您云变量库的人，都应该将这些情况记录下来（比如记录在错误信息中）而不是选择忽略它们：

- WebSocket 关闭代码。所有 4XXX 类代码都[列在该表格中](https://github.com/TurboWarp/cloud-server/blob/master/doc/protocol.md#server---client)。您更倾向于看到`连接已关闭`还是`连接因代码 4002 而关闭`？在表格中查找后者对应的代码后可以清楚地得知问题出在用户名上。
- 从服务器接收到的无效 JSON。如果服务器除了根据关闭代码表所描述的内容之外还有其他需要告知您的信息，它大概会用一句英文发送给您，而不是 JSON 对象。当您的 JSON 解析器出错时，您应该记录从服务器接收到的原始文本，这样您就能获得类似于`从服务器接收到无效 JSON：The cloud data library you are using is putting your Scratch account at risk by sending us your login token for no reason`这样的错误信息，而不是`JSON.parse: unexpected character at line 1 column 1 of the JSON data`。您莫非想看到后者？