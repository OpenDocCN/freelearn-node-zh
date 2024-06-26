# 前言

Node.js 是使用 JavaScript 构建服务器应用程序的快速增长平台。现在它在生产环境中的使用越来越广泛，Node.js 应用程序将开始受到特定的安全漏洞攻击。保护您的用户将需要了解 Node.js 独有的攻击向量以及与其他 Web 应用程序平台共享的攻击向量。

# 本书涵盖的内容

第一章, *Node.js 简介*，介绍了 Node.js 并解释了它与其他开发平台的不同之处。

第二章, *一般考虑*，介绍了一般的安全考虑，特别是 JavaScript 本身以及 Node.js 应用程序的安全考虑。

第三章, *应用考虑*，涉及了与应用程序相关的安全问题，包括身份验证、授权和错误处理。

第四章, *请求层考虑*，涵盖了特定于请求处理的漏洞，例如**跨站请求伪造**（**CSRF**）。

第五章, *响应层漏洞*，处理了在响应处理期间或之后出现的问题，例如**跨站脚本**（**XSS**）。

为了充分利用本书，您应该在系统上安装 Node.js。有关许多平台的说明，请访问[`nodejs.org/`](http://nodejs.org/)。熟悉 npm 及其命令行用法。它与 Node.js 捆绑在一起，因此不需要额外安装。

# 这本书是为谁准备的

本书旨在帮助开发人员保护其 Node.js 应用程序，无论他们是已经在生产中使用它，还是考虑将其用于下一个项目。了解 JavaScript 是前提条件，建议具有一些 Node.js 的经验，但不是必需的。

# 约定

在本书中，您将找到许多不同类型信息的文本样式。以下是一些这些样式的示例，以及它们的含义解释。

文本中的代码单词显示如下：“应该注意`EventEmitter`对象在错误事件方面具有非常特定的行为。”

代码块设置如下：

```js
function sayHello(name) {
       "use strict"; // enables strict mode for this function scope
      console.log("hello", name);
}
```

### 注意

警告或重要说明显示在这样的框中。

### 提示

提示和技巧显示如下。
