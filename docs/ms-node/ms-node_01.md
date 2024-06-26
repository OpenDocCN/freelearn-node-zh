# 第一章：了解节点环境

# 介绍 - JavaScript 作为系统语言

当 John Bardeen、Walter Brattain 和 William Shockley 于 1947 年发明了晶体管时，他们以至今仍在发现的方式改变了世界。从他们的革命性基石开始，工程师可以设计和制造比之前可能的数字电路复杂得多的数字电路。随后的每一个十年都见证了这些设备的新一代：更小、更快、更便宜，通常是数量级的提升。

到了 20 世纪 70 年代，公司和大学能够负担得起足够小以适合单个房间的大型计算机，并且足够强大，可以同时为多个用户提供服务。小型计算机是一种新的、不同类型的设备，需要新的和不同类型的技术来帮助用户充分利用这台机器。贝尔实验室的 Ken Thompson 和 Dennis Ritchie 开发了 Unix 操作系统和编程语言 C 来编写它。他们在系统中构建了进程、线程、流和分层文件系统等结构。今天，这些结构是如此熟悉，以至于很难想象计算机以其他方式工作。然而，它们只是由这些先驱者构建的结构，旨在帮助像我们这样的人理解内存和存储器中的数据模式。

C 是一种系统语言，对于熟悉输入汇编指令的开发人员来说，它是一种安全且功能强大的简写替代方案。在微处理器的熟悉环境中，C 使得低级系统任务变得容易。例如，你可以搜索一个内存块以找到特定值的字节。

```js
// find-byte.c 
int find_byte(const char *buffer, int size, const char b) {
   for (int i = 0; i < size; i++) {
         if (buffer[i] == b) {
               return i;
         }
   }
   return -1; 
}
```

到了 20 世纪 90 年代，我们可以用晶体管构建的东西再次发生了变化。个人电脑（PC）足够轻便和便宜，可以在工作场所和宿舍的桌面上找到。提高的速度和容量使用户可以从仅字符的电传打印机引导到具有漂亮字体和彩色图像的图形环境。通过以太网卡和电缆，你的计算机可以在互联网上获得静态 IP 地址，网络程序可以连接并与地球上的任何其他计算机发送和接收数据。

正是在这样的技术背景下，Sir Tim Berners-Lee 发明了万维网，Brendan Eich 创建了 JavaScript。JavaScript 是为熟悉 HTML 标签的程序员设计的，它是一种超越静态文本页面的动画和交互的方式。在网页的熟悉环境中，JavaScript 使得高级任务变得容易。网页充满了文本和标签，因此合并两个字符串很容易。

```js
// combine-text.js
const s1 = "first string";
const s2 = "second string";
let s3 = s1 + s2;
```

现在，让我们将每个程序移植到另一种语言和平台。首先，从之前的`combine-text.js`，让我们编写`combine-text.c`：

```js
// combine-text.c 
const char *s1 = "first string";
const char *s2 = "second string";
int size = strlen(s1) + strlen(s2);
char *buffer = (char *)malloc(size + 1); // One more for the 0x00 byte that terminates strings 
strcpy(buffer, s1);
strcat(buffer, s2);
free(buffer); // Never forget to free memory!
```

两个字符串文字很容易定义，但之后就变得更加困难。没有自动内存管理，作为开发人员，你需要确定需要多少内存，从系统中分配内存，写入数据而不覆盖缓冲区，然后在之后释放它。

其次，让我们尝试相反的操作：从之前的`find-byte.c`代码，让我们编写`find-byte.js`。在 Node 之前，不可能使用 JavaScript 来搜索特定字节的内存块。在浏览器中，JavaScript 无法分配缓冲区，甚至没有字节类型。但是在 Node 中，这既可能又容易。

```js
// find-byte.js
function find_byte(buffer, b) {
  let i;
  for (i = 0; i < buffer.length; i++) {
    if (buffer[i] == b) {
      return i;
    }
  }
  return -1; // Not found
}
let buffer = Buffer.from("ascii A is byte value sixty-five", "utf8");
let r = find_byte(buffer, 65); // Find the first byte with value 65
console.log(r); // 6 bytes into the buffer
```

从相隔几十年的计算机和人们使用它们的方式的计算机世代中出现，驱动这两种语言 C 和 JavaScript 的设计、目的或用途本来没有必然要结合在一起的真正原因。但它们确实结合在一起了，因为在 2008 年谷歌发布了 Chrome，2009 年 Ryan Dahl 编写了 Node.js。

应用之前仅用于操作系统的设计原则。Chrome 使用多个进程来渲染不同的标签，确保它们的隔离。Chrome 是开源发布的，构建在 WebKit 上，但其中的一部分是全新的。在丹麦的农舍里从头开始编码，Lars Bak 的 V8 使用隐藏类转换、增量垃圾收集和动态代码生成来执行（而不是解释）比以往更快的 JavaScript。

在 V8 的支持下，Node 可以多快地运行 JavaScript？让我们编写一个小程序来展示执行速度：

```js
// speed-loop.js
function main() {
  const cycles = 1000000000;
  let start = Date.now();
  for (let i = 0; i < cycles; i++) {
    /* Empty loop */
  }
  let end = Date.now();
  let duration = (end - start) / 1000;
  console.log("JavaScript looped %d times in %d seconds", cycles, duration);
}
main();
```

以下是`speed-loop.js`的输出：

```js
$ node --version
v9.3.0
$ node speed-loop.js
JavaScript looped 1000000000 times in 0.635 seconds
```

在`for`循环的主体中没有代码，但是您的处理器正在忙于递增`i`，将其与`cycles`进行比较，并重复这个过程。我写这篇文章时已经是 2017 年末了，我用的是一台配备 2.8 GHz 英特尔酷睿 i7 处理器的 MacBook Pro。Node v9.3.0 是当前版本，循环*十亿*次只需要*不到一秒*。

纯 C 有多快？让我们看看：

```js
/* speed-loop.c */
#include <stdio.h>
#include <time.h>
int main() {
  int cycles = 1000000000;
  clock_t start, end;
  double duration;
  start = clock();
  for (int i = 0; i < cycles; i++) {
    /* Empty loop */
  }
  end = clock();
  duration = ((double)(end - start)) / CLOCKS_PER_SEC;
  printf("C looped %d times in %lf seconds\n", cycles,duration);
  return 0;
}
```

以下是`speed-loop.c`的输出：

```js
$ gcc --version
Apple LLVM version 8.1.0 (clang-802.0.42)
$ gcc speed-loop.c -o speed-loop
$ ./speed-loop
C looped 1000000000 times in 2.398294 seconds
```

为了进行额外的比较，让我们尝试一种解释性语言，比如 Python：

```js
# speed-loop.py

import time

def main():

  cycles = 1000000000
  start = time.perf_counter()

  for i in range(0, cycles):
    pass # Empty loop

  end = time.perf_counter()
  duration = end - start
  print("Python looped %d times in %.3f seconds" % (cycles, duration))

main()
```

以下是`speed-loop.py`的输出：

```js
$ python3 --version
Python 3.6.1
$ python3 speed-loop.py
Python looped 1000000000 times in 31.096 seconds
```

Node 运行的速度足够快，以至于您不必担心您的应用程序可能会因执行速度而变慢。当然，您仍然需要考虑性能，但受到语言和平台选择以外的因素的限制，比如算法、I/O 和外部进程、服务和 API。由于 V8 编译 JavaScript 而不是解释它，Node 让您享受高级语言特性，如自动内存管理和动态类型，而无需放弃本地编译二进制的性能。以前，您必须选择其中一个；但现在，您可以两者兼得。这太棒了。

20 世纪 70 年代的计算是关于微处理器的，20 世纪 90 年代的计算是关于网页的。今天，2017 年，另一代新的物理计算技术再次改变了我们的机器。您口袋里的智能手机通过无线方式与云中的可扩展的按需付费软件服务进行通信。这些服务在 Unix 的虚拟化实例上运行，Unix 又在数据中心的物理硬件上运行，其中一些数据中心非常大，被策略性地放置在附近的水电站中获取电流。有了这样新颖和不同的机器，我们不应该感到惊讶，用户的可能性和开发人员的必要性也是新的和不同的，再次。

Node.js 将 JavaScript 想象成一个类似于 C 的系统语言。在网页上，JavaScript 可以操作头部和样式。作为系统语言，JavaScript 可以操作内存缓冲区、进程和流、文件和套接字。这种时代错位是由 V8 的性能所可能的，它将语言发送回 20 年前，将其从网页移植到微处理器芯片上。

“Node 的目标是提供一种简单的方式来构建可扩展的网络程序。”

- Node.js 的创始人 Ryan Dahl

在本书中，我们将学习专业 Node 开发人员用来解决当今软件挑战的技术。通过掌握 Node，您正在学习如何构建下一代软件。在本章中，我们将探讨 Node 应用程序的设计方式，以及它在服务器上的印记的形状和质地，以及 Node 为开发人员提供的强大的基本工具和功能集。在整个过程中，我们将逐渐探讨更复杂的示例，展示 Node 简单、全面和一致的架构如何很好地解决许多困难的问题。

# Unix 的设计哲学

随着网络应用程序规模的扩大，它必须识别、组织和维护的信息量也在增加。这种信息量，以 I/O 流、内存使用和处理器负载的形式，随着更多的客户端连接而扩大。这种信息量的扩大也给软件开发人员带来了负担。通常出现扩展问题，通常表现为无法准确预测大型系统的行为，从而导致其较小的前身的行为失败：

+   一个为存储几千条记录设计的数据层能容纳几百万条记录吗？

+   用于搜索少量记录的算法是否足够高效，可以搜索更多记录吗？

+   这个服务器能处理 10000 个同时的客户端连接吗？

创新的边缘是锋利的，切割迅速，给人更少的时间来思考，特别是当错误的代价被放大时。构成应用程序整体的对象的形状变得模糊且难以理解，特别是当对系统中的动态张力做出反应性的临时修改时。在规范中描述为一个小子系统的东西可能已经被补丁到了许多其他系统中，以至于其实际边界被误解。当这种情况发生时，准确追踪整体复合部分的轮廓就变得不可能了。

最终，一个应用程序变得不可预测。当一个人无法预测应用程序的所有未来状态或变化的副作用时，这是危险的。许多服务器、编程语言、硬件架构、管理风格等等，都试图克服随着增长而带来的风险问题，失败威胁着成功。通常情况下，更复杂的系统被作为解决方案出售。任何一个人对信息的掌握都是脆弱的。复杂性随着规模而增加；混乱随着复杂性而来。随着分辨率变得模糊，错误就会发生。

Node 选择了清晰和简单，回应了几十年前的一种哲学：

"编写程序，做一件事，并且做得很好。

编写程序以便协同工作。

编写处理文本流的程序，因为这是一个通用的接口。

-Peter H. Salus，《Unix 四分之一世纪》，1994

从他们创建和维护 Unix 的经验中，*Ken Thompson*和*Dennis Ritchie*提出了一个关于人们如何最好构建软件的哲学。*Ryan Dahl*在 Node 的设计中遵循这一哲学，做出了许多决定：

+   Node 的设计偏向简单而不是复杂

+   Node 使用熟悉的 POSIX API，而不是试图改进

+   Node 使用事件来完成所有操作，不需要线程

+   Node 利用现有的 C 库，而不是试图重新实现它们的功能

+   Node 偏向文本而不是二进制格式

文本流是 Unix 程序的语言。JavaScript 从一开始就擅长处理文本，作为一种 Web 脚本语言。这是一个自然的匹配。

# POSIX

**POSIX**，**可移植操作系统接口**，定义了 Unix 的标准 API。它被采用在基于 Unix 的操作系统和其他系统中。IEEE 创建并维护 POSIX 标准，以使来自不同制造商的系统兼容。在运行 macOS 的笔记本电脑上使用 POSIX API 编写 C 程序，以后在树莓派上构建它会更容易。

作为一个共同的基准，POSIX 古老、简单，最重要的是，所有类型的开发人员都熟悉。在 C 程序中创建一个新目录，使用这个 API：

```js
int mkdir(const char *path, mode_t mode);
```

这就是 Node 的特点：

```js
fs.mkdir(path[, mode], callback)
```

文件系统模块的 Node 文档一开始就告诉开发人员，这里没有什么新东西：

文件 I/O 是通过标准 POSIX 函数的简单包装提供的。

[`nodejs.org/api/fs.html`](https://nodejs.org/api/fs.html)

对于 Node 来说，*Ryan Dahl*实现了经过验证的 POSIX API，而不是试图自己想出一些东西。虽然在某些方面或某些情况下，这样的尝试可能更好，但它会失去 POSIX 给其他系统训练有素的新 Node 开发人员带来的即时熟悉感。

通过选择 POSIX 作为 API，Node 并不受限于上世纪 70 年代的标准。任何人都可以轻松编写自己的模块，调用 Node 的 API，同时向上呈现不同的 API。这些更高级的替代方案可以在达尔文式的竞争中证明自己比 POSIX 更好。

# 一切皆事件

如果程序要求操作系统在磁盘上打开一个文件，这个任务可能会立即完成。或者，磁盘可能需要一段时间才能启动，或者操作系统正在处理其他文件系统活动，需要等待才能执行新的请求。超越应用程序进程空间内存操作的任务，涉及到计算机、网络和互联网中更远的硬件，无法以相同的方式快速或可靠地进行编程。软件设计师需要一种方法来编写这些可能缓慢和不可靠的任务，而不会使他们的应用程序整体变得缓慢和不可靠。对于使用 C 和 Java 等语言的系统程序员来说，解决这个问题的标准和公认的工具是线程。

```js
pthread_t my_thread;
int x = 0;
/* Make a thread and have it run my_function(&x) */
pthread_create(&my_thread, NULL, my_function, &x);
```

如果程序向用户提问，用户可能会立即回答。或者，用户可能需要一段时间来思考，然后再点击“是”或“否”。对于使用 HTML 和 JavaScript 的 Web 开发人员，这样做的方法是事件，如下所示：

```js
<button onclick="myFunction()">Click me</button>
```

乍一看，这两种情况可能看起来完全不同：

+   在第一种情况下，低级系统正在将内存块从程序传输到程序，毫秒的延迟可能太大而无法测量

+   在第二种情况下，一个巨大的软件堆栈的顶层正在向用户提问

然而，在概念上，它们是相同的。Node 的设计意识到了这一点，并且在两者都使用了事件。在 Node 中，有一个线程，绑定到一个事件循环。延迟任务被封装，通过回调函数进入和退出执行上下文。I/O 操作生成事件数据流，并通过单个堆栈进行传输。并发由系统管理，抽象出线程池，并简化对内存的共享访问。

Node 向我们展示了 JavaScript 作为系统语言并不需要线程。此外，通过不使用线程，JavaScript 和 Node 避免了并发问题，这些问题会给开发人员带来性能和可靠性挑战，即使是对于熟悉代码库的开发人员也可能难以理解。在《第二章》《理解异步事件驱动编程》中，我们将深入探讨事件和事件循环。

# 标准库

Node 是建立在标准开源 C 库上的。例如，*TLS*和*SSL*协议是由*OpenSSL*实现的。不仅仅是采用 API，OpenSSL 的 C 源代码也包含在 Node 中并编译进去。当你的 JavaScript 程序对加密密钥进行哈希处理时，实际上并不是 JavaScript 在进行工作。你的 JavaScript 通过 Node 调用了 OpenSSL 的 C 代码。实质上，你在对本地库进行脚本编写。

使用现有和经过验证的开源库的设计选择帮助了 Node 的多个方面：

+   这意味着 Node 可以迅速出现在舞台上，具有系统程序员需要和期望的核心功能，这些功能已经存在。

+   它确保性能、可靠性和安全性与库相匹配

+   它也没有破坏跨平台使用，因为所有这些 C 库都已经被编写和维护多年，可以编译到不同的架构上

以前的平台和语言在努力实现软件可移植性时做出了不同的选择。例如，*100% Pure Java™ Standard*是*Sun Microsystems*的一个倡议，旨在促进可移植应用程序的开发。与其利用混合堆栈中的现有代码，它鼓励开发人员在 Java 中重写所有内容。开发人员必须通过编写和测试新代码来保持功能、性能和安全性达到标准。另一方面，Node 选择了一种设计，可以免费获得所有这些功能。

# 扩展 JavaScript

当他设计 Node 时，JavaScript 并不是*Ryan Dahl*的最初语言选择。然而，经过探索，他发现了一种现代语言，没有对流、文件系统、处理二进制对象、进程、网络等功能的看法。JavaScript 严格限制在浏览器中，对于这些功能没有用处，也没有实现这些功能。

受 Unix 哲学的指导，达尔坚持了一些严格的原则：

+   Node 程序/进程在单个线程上运行，通过事件循环来排序执行

+   Web 应用程序具有大量 I/O 操作，因此重点应该放在加快 I/O 上

+   程序流程总是通过异步回调来指导

+   昂贵的 CPU 操作应该拆分成单独的并行进程，并在结果到达时发出事件

+   复杂的程序应该由简单的程序组装而成

总的原则是，操作绝对不能阻塞。Node 对速度（高并发）和效率（最小资源使用）的渴望要求减少浪费。等待过程是一种浪费，特别是在等待 I/O 时。

JavaScript 的异步、事件驱动设计完全符合这一模式。应用程序表达对未来某个事件的兴趣，并在该事件发生时得到通知。这种常见的 JavaScript 模式应该对你来说很熟悉：

```js
Window.onload = function() {
  // When all requested document resources are loaded,
  // do something with the resulting environment
}
element.onclick = function() {
  // Do something when the user clicks on this element
}
```

I/O 操作完成所需的时间是未知的，因此模式是在发出 I/O 事件时请求通知，无论何时发生，都允许其他操作在此期间完成。

Node 为 JavaScript 添加了大量新功能。主要是提供了事件驱动的 I/O 库，为开发人员提供了系统访问权限，这是浏览器中的 JavaScript 无法做到的，比如写入文件系统或打开另一个系统进程。此外，该环境被设计为模块化，允许将复杂的程序组装成更小更简单的组件。

让我们看看 Node 如何导入 JavaScript 的事件模型，扩展它，并在创建强大系统命令的接口时使用它。

# 事件

Node API 中的许多函数会发出事件。这些事件是`events.EventEmitter`的实例。任何对象都可以扩展`EventEmitter`，为 Node 开发人员提供了一种简单而统一的方式来构建紧密的异步接口以调用对象方法。

以下代码将 Node 的`EventEmitter`对象设置为我们定义的函数构造函数的原型。每个构造的实例都将`EventEmitter`对象暴露给其原型链，提供对事件 API 的自然引用。计数器实例方法会发出事件，然后监听它们。创建一个`Counter`后，我们监听增加的事件，指定一个回调，Node 在事件发生时会调用它。然后，我们调用增加两次。每次，我们的`Counter`都会增加它持有的内部计数，然后发出增加的事件。这将调用我们的回调，将当前计数传递给它，我们的回调会将其记录下来：

```js
// File counter.js
// Load Node's 'events' module, and point directly to EventEmitter there
const EventEmitter = require('events').EventEmitter;
// Define our Counter function
const Counter = function(i) { // Takes a starting number
  this.increment = function() { // The counter's increment method
    i++; // Increment the count we hold
    this.emit('incremented', i); // Emit an event named incremented
  }
}
// Base our Counter on Node's EventEmitter
Counter.prototype = new EventEmitter(); // We did this afterwards, not before!
// Now that we've defined our objects, let's see them in action
// Make a new Counter starting at 10
const counter = new Counter(10);
// Define a callback function which logs the number n you give it
const callback = function(n) {
  console.log(n);
}
// Counter is an EventEmitter, so it comes with addListener
counter.addListener('incremented', callback);
counter.increment(); // 11
counter.increment(); // 12
```

以下是`counter.js`的输出：

```js
$ node counter.js
11
12
```

要删除绑定到`counter`的事件侦听器，请使用此代码：

```js
counter.removeListener('incremented', callback).
```

为了与基于浏览器的 JavaScript 保持一致，`counter.on`和`counter.addListener`是可以互换的。

Node 将`EventEmitter`引入 JavaScript，并使其成为你的对象可以扩展的对象。这大大增加了开发人员的可能性。使用`EventEmitter`，Node 可以以事件导向的方式处理 I/O 数据流，执行长时间运行的任务，同时保持 Node 异步、非阻塞编程的原则：

```js
// File stream.js
// Use Node's stream module, and get Readable inside
let Readable = require('stream').Readable;
// Make our own readable stream, named r
let r = new Readable;
// Start the count at 0
let count = 0;
// Downstream code will call r's _read function when it wants some data from r
r._read = function() {
  count++;
  if (count > 10) { // After our count has grown beyond 10
    return r.push(null); // Push null downstream to signal we've got no more data
  }
  setTimeout(() => r.push(count + '\n'), 500); // A half second from now, push our count on a line
};
// Have our readable send the data it produces to standard out
r.pipe(process.stdout);
```

以下是`stream.js`的输出：

```js
$ node stream.js
1
2
3
4
5
6
7
8
9
10
```

这个例子创建了一个可读流`r`，并将其输出传输到标准输出。每 500 毫秒，代码会递增一个计数器，并将带有当前计数的文本行推送到下游。尝试自己运行程序，你会看到一系列数字出现在你的终端上。

在第 11 次计数时，`r`将 null 推送到下游，表示它没有更多的数据要发送。这关闭了流，而且没有更多的事情要做，Node 退出了进程。

后续章节将更详细地解释流。在这里，只需注意将数据推送到流上会触发一个事件，你可以分配一个自定义回调来处理这个事件，以及数据如何向下游流动。

Node 一贯将 I/O 操作实现为异步的、事件驱动的数据流。这种设计选择使得 Node 具有出色的性能。与为长时间运行的任务（如文件上传）创建线程（或启动整个进程）不同，Node 只需要投入资源来处理回调。此外，在流推送数据的短暂时刻之间的长时间段内，Node 的事件循环可以自由地处理其他指令。

作为练习，重新实现`stream.js`，将`r`产生的数据发送到文件而不是终端。你需要使用 Node 的`fs.createWriteStream`创建一个新的可写流`w`：

```js
// File stream2file.js
// Bring in Node's file system module
const fs = require('fs');
// Make the file counter.txt we can fill by writing data to writeable stream w
const w = fs.createWriteStream('./counter.txt', { flags: 'w', mode: 0666 });
...
// Put w beneath r instead
r.pipe(w);
```

# 模块化

在他的书《Unix 编程艺术》中，Eric Raymond 提出了**模块化原则**：

“开发人员应该通过明确定义的接口将程序构建成由简单部分连接而成的程序，这样问题就是局部的，程序的部分可以在未来版本中被替换以支持新功能。这个原则旨在节省调试复杂、冗长和难以阅读的代码的时间。”

大型系统很难理解，特别是当内部组件的边界模糊不清，它们之间的交互又很复杂时。将大型系统构建成由小的、简单的、松耦合的部分组成的原则对软件和其他领域都是一个好主意。物理制造、管理理论、教育和政府都受益于这种设计哲学。

当开发人员开始将 JavaScript 用于更大规模和更复杂的软件挑战时，他们遇到了这个挑战。还没有一个好的方法（后来也没有一个通用的标准方法）来从更小的程序组装 JavaScript 程序。例如，你可能在顶部看到带有这些标签的 HTML 页面：

```js
<head>
<script src="img/fileA.js"></script>
<script src="img/fileB.js"></script>
<script src="img/fileC.js"></script>
<script src="img/fileD.js"></script>
...
</head>
```

这种方法虽然有效，但会导致一系列问题：

+   页面必须在需要或使用任何依赖之前声明所有潜在的依赖。如果在运行过程中，你的程序遇到需要额外依赖的情况，动态加载另一个模块是可能的，但是是一种单独的黑客行为。

+   脚本没有封装。每个文件中的代码都写入同一个全局对象。添加新的依赖可能会因为名称冲突而破坏之前的依赖。

+   `fileA`无法将`fileB`作为一个集合来处理。像`fileB.function1`这样的可寻址上下文是不可用的。

`<script>`标签可能是一个很好的地方，用于提供诸如依赖关系意识和版本控制等有用的模块服务，但它并没有这些功能。

这些困难和危险使得创建和使用 JavaScript 模块感觉比轻松更加危险。一个具有封装和版本控制等功能的良好模块系统可以扭转这一局面，鼓励代码组织和共享，并导致一个高质量的开源软件组件生态系统。

JavaScript 需要一种标准的方式来加载和共享离散的程序模块，在 2009 年找到了 CommonJS 模块规范。Node 遵循这个规范，使得定义和共享被称为**模块**或**包**的可重用代码变得容易。

选择了一个简单而令人愉悦的设计，一个包就是一个 JavaScript 文件的目录。关于包的元数据，比如它的名称、版本和软件许可证，存储在一个名为`package.json`的额外文件中。这个文件的 JSON 内容既容易被人类阅读，也容易被机器读取。让我们来看一下：

```js
{
  "name": "mypackage1",
  "version": "0.1.2",
  "dependencies": {
    "jquery": "³.1.0",
    "bluebird": "³.4.1",
  },
  "license": "MIT"
}
```

这个`package.json`定义了一个名为`mypackage1`的包，它依赖于另外两个包：**jQuery**和**Bluebird**。在包名旁边是一个版本号。版本号遵循**语义化版本（SemVer）**规则，格式为主版本号.次版本号.修订版本号。查看你的代码正在使用的包的递增版本号，这就是它的含义：

+   **主要版本：**API 的目的或结果发生了变化。如果你的代码调用了更新的函数，可能会出现错误或产生意外的结果。找出发生了什么变化，并确定它是否影响了你的代码。

+   **次要版本：**包增加了功能，但仍然兼容。运行所有的测试，然后就可以使用了。如果你感兴趣，可以查看文档，因为可能会有新的、更高级的 API 部分，以及你熟悉的函数和对象。

+   **修订版本：**包修复了一个 bug，提高了性能，或者进行了一些重构。运行所有的测试，然后就可以使用了。

包使得可以从许多小的、相互依赖的系统构建大型系统。也许更重要的是，包鼓励分享。关于 SemVer 的更详细信息可以在附录 A 中找到，*将你的工作组织成模块*，在那里更深入地讨论了 npm 和包。

“我在这里描述的不是一个技术问题。这是一群人聚在一起做出决定，迈出一步，开始一起构建更大更酷的东西。”

– Kevin Dangoor，CommonJS 的创始人

CommonJS 不仅仅是关于模块，实际上它是一整套标准，旨在消除一切阻碍 JavaScript 成为世界主导语言的东西，开源开发者*Kris Kowal*在 2009 年的一篇文章中解释了这一点。他将这些障碍中的第一个称为缺乏一个良好的模块系统。第二个障碍是缺乏一个标准库，包括文件系统的访问、I/O 流的操作，以及字节和二进制数据块的类型。如今，CommonJS 以给 JavaScript 提供了一个模块系统而闻名，而 Node 则是给了 JavaScript 系统级的访问：

[`arstechnica.com/information-technology/2009/12/commonjs-effort-sets-javascript-on-path-for-world-domination/`](https://arstechnica.com/information-technology/2009/12/commonjs-effort-sets-javascript-on-path-for-world-domination/)

**CommonJS**给了 JavaScript 包。有了包之后，JavaScript 需要的下一件事就是包管理器。Node 提供了 npm。

npm 作为包的注册表有两种访问方式。首先，在网站[www.npmjs.com](http://www.npmjs.com)，你可以链接和搜索包，基本上是在寻找合适的包。统计数据显示了包在过去一天、一周和一个月内被下载的次数，展示了它的受欢迎程度和使用情况。大多数包都链接到开发者的个人资料页面和 GitHub 上的开源代码，这样你就可以看到代码，了解最近的开发活动，并评判作者和贡献者的声誉。

访问 npm 的第二种方式是通过与 Node 一起安装的命令行工具 npm。使用 npm 作为工作站的传统软件包管理器，您可以全局安装软件包，在 shell 的路径上创建新的命令行工具。npm 还知道如何创建、读取和编辑`package.json`文件，并可以为您创建一个新的、空的 Node 软件包，添加它所需的依赖项，下载所有的代码，并保持一切更新。

除了 Git 和 GitHub，npm 现在正在实现上世纪 70 年代确定的软件开发梦想：代码可以更频繁地被重复使用，软件项目不需要经常从头开始编写。

早期尝试通过 CVS 和 Subversion 等版本控制系统以及像[SourceForge.net](http://SourceForge.net)这样的开源代码共享网站来实现这一目标，侧重于更大的代码和人员单位，并没有取得太多成果。

GitHub 和 npm 在两个重要方面采取了不同的方法：

+   更看重独立开发者的个人工作而不是社区会议和讨论，开发者可以更多地专注于代码而不是对话

+   偏爱小型、原子化的软件组件而不是完整的应用程序，封装的组合不仅发生在子例程和对象的微观层面，而且在更重要的应用程序设计的宏观层面上也发生了。

即使文档也可以通过新的方法变得更好：在单片软件应用程序中，文档往往是产品发货后可能发生或可能不会发生的事后想法。

对于组件，出色的文档对于向世界推销您的软件包是必不可少的，使其每天获得更多的公共下载量，并且作为开发者保持的社交媒体账户也会有更多的关注者。

Node 的成功在很大程度上归功于作为 Node 开发者可用的软件包的数量和质量。

有关创建和管理 Node 软件包的更详细信息可以在*附录 A，将您的工作组织成模块*中找到。

要遵循的关键设计理念是：尽可能使用软件包构建程序，并在可能的情况下共享这些软件包。您的应用程序的形状将更清晰，更易于维护。重要的是，成千上万的其他开发人员的努力可以通过 npm 直接包含到应用程序中，并且间接地通过共享软件包由 Node 社区的成员测试、改进、重构和重新利用。

与流行观念相反，npm 并不是 Node Package Manager 的缩写，*绝不应该被用作或解释为首字母缩写*：

[`docs.npmjs.com/policies/trademark`](https://docs.npmjs.com/policies/trademark)

# 网络

浏览器中的 I/O 受到严格限制，这是有很好的原因的——如果任何给定网站上的 JavaScript 可以访问您的文件系统，例如，用户只能点击他们信任的新网站的链接，而不是他们只是想尝试的网站。通过将页面保持在有限的沙盒中，Web 的设计使得从 thing1.com 导航到 thing2.com 不会像双击 thing1.exe 和 thing2.exe 那样产生后果。

当然，Node 将 JavaScript 重新塑造为系统语言，使其直接且无障碍地访问操作系统内核对象，如文件、套接字和进程。这使得 Node 可以创建具有高 I/O 需求的可扩展系统。很可能你在 Node 中编写的第一件事是一个 HTTP 服务器。

Node 支持标准的网络协议，除了 HTTP，还有 TLS/SSL 和 UDP。借助这些工具，我们可以轻松地构建可扩展的网络程序，远远超出了 JavaScript 开发人员从浏览器中了解的相对有限的 AJAX 解决方案。

让我们编写一个简单的程序，向另一个节点发送一个 UDP 数据包：

```js
const dgram = require('dgram');
let client = dgram.createSocket("udp4");
let server = dgram.createSocket("udp4");
let message = process.argv[2] || "message";
message = Buffer.from(message);
server
.on('message', msg => {
  process.stdout.write(`Got message: ${msg}\n`);
  process.exit();
})
.bind(41234);
client.send(message, 0, message.length, 41234, "localhost");
```

打开两个终端窗口，分别导航到您的代码包的第八章下的“扩展应用程序”文件夹。现在我们将在一个窗口中运行 UDP 服务器，在另一个窗口中运行 UDP 客户端。

在右侧窗口中，使用以下命令运行`receive.js`：

```js
$ node receive.js
```

在左侧，使用以下命令运行`send.js`：

```js
$ node send.js
```

执行该命令将导致右侧出现消息：

```js
$ node receive.js
Message received!
```

UDP 服务器是`EventEmitter`的一个实例，在绑定端口接收到消息时会发出消息事件。使用 Node，您可以使用 JavaScript 在 I/O 级别编写应用程序，轻松移动数据包和二进制数据流。

让我们继续探索 I/O、进程对象和事件。首先，让我们深入了解 Node 核心的机器 V8。

# V8、JavaScript 和优化

V8 是谷歌的 JavaScript 引擎，用 C++编写。它在虚拟机（Virtual Machine）内部编译和执行 JavaScript 代码。当加载到谷歌 Chrome 中的网页展示某种动态效果，比如自动更新列表或新闻源时，您看到的是由 V8 编译的 JavaScript 在工作。

V8 管理 Node 的主进程线程。在执行 JavaScript 时，V8 会在自己的进程中执行，其内部行为不受 Node 控制。在本节中，我们将研究通过使用这些选项来获得的性能优势，学习如何编写可优化的 JavaScript，以及最新 Node 版本（例如 9.x，我们在本书中使用的版本）用户可用的尖端 JavaScript 功能。

# 标志

有许多可用于操纵 Node 运行时的设置。尝试这个命令：

```js
$ node -h
```

除了`--version`等标准选项外，您还可以将 Node 标记为`--abort-on-uncaught-exception`。

您还可以列出 v8 可用的选项：

```js
$ node --v8-options
```

其中一些设置可以帮助您度过难关。例如，如果您在像树莓派这样的受限环境中运行 Node，您可能希望限制 Node 进程可以消耗的内存量，以避免内存峰值。在这种情况下，您可能希望将`--max_old_space_size`（默认约 1.5GB）设置为几百 MB。

您可以使用`-e`参数将 Node 程序作为字符串执行；在这种情况下，记录出您的 Node 副本包含的 V8 版本：

```js
$ node –e "console.log(process.versions.v8)"
```

值得您花时间尝试 Node/V8 的设置，既可以提高效用，也可以让您对发生的事情（或可能发生的事情）有更深入的了解。

# 优化您的代码

智能代码设计的简单优化确实可以帮助您。传统上，在浏览器中工作的 JavaScript 开发人员不需要关注内存使用优化，因为通常对于通常不复杂的程序来说，他们有很多内存可用。在服务器上，情况就不同了。程序通常更加复杂，耗尽内存会导致服务器崩溃。

动态语言的便利之处在于避免了编译语言所施加的严格性。例如，您无需明确定义对象属性类型，并且实际上可以随意更改这些属性类型。这种动态性使得传统编译变得不可能，但为 JavaScript 等探索性语言开辟了一些有趣的新机会。然而，与静态编译语言相比，动态性在执行速度方面引入了显著的惩罚。JavaScript 的有限速度经常被认为是其主要弱点之一。

V8 试图为 JavaScript 实现编译语言所观察到的速度。V8 将 JavaScript 编译为本机机器代码，而不是解释字节码，或使用其他即时技术。由于 JavaScript 程序的精确运行时拓扑无法提前知道（语言是动态的），编译包括两阶段的推测性方法：

1.  最初，第一遍编译器（*完整*编译器）尽快将您的代码转换为可运行状态。在此步骤中，类型分析和代码的其他详细分析被推迟，优先考虑快速编译-您的 JavaScript 可以尽可能接近即时执行。进一步的优化是在第二步完成的。

1.  一旦程序启动运行，优化编译器就开始监视程序的运行方式，并尝试确定其当前和未来的运行时特性，根据需要进行优化和重新优化。例如，如果某个函数以一致类型的相似参数被调用了成千上万次，V8 将使用基于乐观假设的优化代码重新编译该函数，假设未来的类型将与过去的类型相似。虽然第一次编译步骤对尚未知和未类型化的功能签名保守，但这个`热`函数的可预测纹理促使 V8 假设某种最佳配置文件，并根据该假设重新编译。

假设可以帮助我们更快地做出决定，但可能会导致错误。如果`热`函数 V8 的编译器只针对某种类型签名进行了优化，现在却使用违反该优化配置文件的参数调用了该函数怎么办？在这种情况下，V8 别无选择：它必须取消优化该函数。V8 必须承认自己的错误，并撤销已经完成的工作。如果看到新的模式，它将在未来重新优化。然而，如果 V8 在以后再次取消优化，并且如果这种优化/取消优化的二进制切换继续，V8 将简单地*放弃*，并将您的代码留在取消优化状态。

让我们看看一些方法来设计和声明数组、对象和函数，以便您能够帮助而不是阻碍编译器。

# 数字和跟踪优化/取消优化

ECMA-262 规范将 Number 值定义为“与双精度 64 位二进制格式 IEEE 754 值对应的原始值”。关键是 JavaScript 中没有整数类型；有一个被定义为双精度浮点数的 Number 类型。

出于性能原因，V8 在内部对所有值使用 32 位数字。这里讨论的技术原因太多，可以说有一位用于指向另一个 32 位数字，如果需要更大的宽度。无论如何，很明显 V8 将数字标记为两种类型的值，并在这些类型之间切换将会花费一些代价。尽量将您的需求限制在可能的情况下使用 31 位有符号整数。

由于 JavaScript 的类型不确定性，允许切换分配给插槽的数字的类型。例如，以下代码不会引发错误：

```js
let a = 7;
a = 7.77;
```

然而，像 V8 这样的推测性编译器将无法优化这个变量赋值，因为它*猜测*`a`将始终是一个整数的假设是错误的，迫使取消优化。

我们可以通过设置一些强大的 V8 选项，执行 Node 程序中的 V8 本机命令，并跟踪 v8 如何优化/取消优化您的代码来演示优化/取消优化过程。

考虑以下 Node 程序：

```js
// program.js
let someFunc = function foo(){}
console.log(%FunctionGetName(someFunc));
```

如果您尝试正常运行此程序，您将收到意外的令牌错误-在 JavaScript 中无法在标识符名称中使用模数（%）符号。带有%前缀的这个奇怪的方法是什么？这是一个 V8 本机命令，我们可以通过使用`--allow-natives-syntax`标志来打开执行这些类型的函数：

```js
node --allow-natives-syntax program.js
// 'someFunc', the function name, is printed to the console.
```

现在，考虑以下代码，它使用本机函数来断言关于平方函数的优化状态的信息，使用`％OptimizeFunctionOnNextCall`本机方法：

```js
let operand = 3;
function square() {
    return operand * operand;
}
// Make first pass to gather type information
square();
// Ask that the next call of #square trigger an optimization attempt;
// Call
%OptimizeFunctionOnNextCall(square);
square();
```

使用上述代码创建一个文件，并使用以下命令执行它：`node --allow-natives-syntax --trace_opt --trace_deopt myfile.js`。您将看到类似以下返回的内容：

```js
 [deoptimize context: c39daf14679]
 [optimizing: square / c39dafca921 - took 1.900, 0.851, 0.000 ms]
```

我们可以看到 V8 在优化平方函数时没有问题，因为操作数只声明一次并且从未改变。现在，将以下行追加到你的文件中，然后再次运行它：

```js
%OptimizeFunctionOnNextCall(square);
operand = 3.01;
square();
```

在这次执行中，根据之前给出的优化报告，你现在应该会收到类似以下的内容：

```js
**** DEOPT: square at bailout #2, address 0x0, frame size 8
 [deoptimizing: begin 0x2493d0fca8d9 square @2]
 ...
 [deoptimizing: end 0x2493d0fca8d9 square => node=3, pc=0x29edb8164b46, state=NO_REGISTERS, alignment=no padding, took 0.033 ms]
 [removing optimized code for: square]
```

这份非常有表现力的优化报告非常清楚地讲述了故事：一度优化的平方函数在我们改变一个数字类型后被取消了优化。鼓励你花一些时间编写代码并使用这些方法进行测试。

# 对象和数组

正如我们在研究数字时所学到的，当你的代码是可预测的时，V8 的工作效果最好。对于数组和对象也是如此。几乎所有以下的*不良实践*之所以不好，是因为它们会造成不可预测性。

记住，在 JavaScript 中，对象和数组在底层非常相似（导致了一些奇怪的规则，给那些取笑这门语言的人提供了无穷无尽的素材！）。我们不会讨论这些差异，只会讨论重要的相似之处，特别是在这两种数据结构如何从类似的优化技术中受益。

避免在数组中混合类型。最好始终保持一致的数据类型，比如*全部整数*或*全部字符串*。同样，尽量避免在数组中改变类型，或者在初始化后改变属性赋值的类型。V8 通过创建隐藏类来跟踪类型来创建对象的*蓝图*，当这些类型改变时，优化蓝图将被销毁并重建——如果你幸运的话。访问[`github.com/v8/v8/wiki/Design%20Elements`](https://github.com/v8/v8/wiki/Design%20Elements)获取更多信息。

不要创建带有间隙的数组，比如以下的例子：

```js
let a = [];
a[2] = 'foo';
a[23] = 'bar';
```

稀疏数组之所以不好，是因为 V8 可以使用非常高效的线性存储策略来存储（和访问）你的数组数据，或者它可以使用哈希表（速度要慢得多）。如果你的数组是稀疏的，V8 必须选择两者中效率较低的那个。出于同样的原因，始终从零索引开始你的数组。同样，永远不要使用*delete*来从数组中删除元素。你只是在那个位置插入一个*undefined*值，这只是创建稀疏数组的另一种方式。同样，要小心用空值填充数组——确保你推入数组的外部数据不是不完整的。

尽量不要预先分配大数组——边用边增长。同样，不要预先分配一个数组然后超出那个大小。你总是希望避免吓到 V8，使其将你的数组转换为哈希表。每当向对象构造函数添加新属性时，V8 都会创建一个新的隐藏类。尽量避免在实例化后添加属性。以相同的顺序在构造函数中初始化所有成员。相同的属性+相同的顺序=相同的对象。

记住，JavaScript 是一种动态语言，允许在实例化后修改对象（和对象原型）。因此，V8 为对象分配内存的方式是怎样的呢？它做出了一些合理的假设。在从给定构造函数实例化一定数量的对象之后（我相信触发数量是 8），假定这些对象中最大的一个是最大尺寸，并且所有后续实例都被分配了那么多的内存（初始对象也被类似地调整大小）。每个实例基于这个假定的最大尺寸被分配了 32 个快速属性槽。任何额外的属性都被放入一个（更慢的）溢出属性数组中，这个数组可以调整大小以容纳任何进一步的新属性。

对于对象和数组，尽量尽可能地定义数据结构的形状，包括一定数量的属性、类型等等，以便*未来*使用。

# 函数

通常经常调用函数，应该是你主要优化的焦点之一。包含 try-catch 结构的函数是不可优化的，包含其他不可预测结构的函数也是不可优化的，比如`with`或`eval`。如果由于某种原因，您的函数无法优化，请尽量减少使用。

一个非常常见的优化错误涉及使用多态函数。接受可变函数参数的函数将被取消优化。避免多态函数。

关于 V8 如何执行推测优化的优秀解释可以在这里找到：[`ponyfoo.com/articles/an-introduction-to-speculative-optimization-in-v8`](https://ponyfoo.com/articles/an-introduction-to-speculative-optimization-in-v8)

# 优化的 JavaScript

JavaScript 语言不断变化，一些重大的变化和改进已经开始进入本机编译器。最新 Node 构建中使用的 V8 引擎支持几乎所有最新功能。调查所有这些超出了本章的范围。在本节中，我们将提到一些最有用的更新以及它们如何简化您的代码，帮助您更容易理解和推理，更易于维护，甚至可能更高效。

在本书中，我们将使用最新的 JavaScript 功能。您可以使用 Promise、Generator 和 async/await 构造，从 Node 8.x 开始，我们将在整本书中使用这些功能。这些并发运算符将在第二章中深入讨论，*理解异步事件驱动编程*，但现在一个很好的收获是，回调模式正在失去其主导地位，特别是 Promise 模式正在主导模块接口。

实际上，最近在 Node 的核心中添加了一个新方法`util.promisify`，它将基于回调的函数转换为基于 Promise 的函数：

```js
const {promisify} = require('util');
const fs = require('fs');

// Promisification happens here
let readFileAsync = promisify(fs.readFile);

let [executable, absPath, target, ...message] = process.argv;

console.log(message.length ? message.join(' ') : `Running file ${absPath} using binary ${executable}`);

readFileAsync(target, {encoding: 'utf8'})
.then(console.log)
.catch(err => {
  let message = err.message;
  console.log(`
    An error occurred!
    Read error: ${message}
  `);
});
```

能够轻松地*promisify* `fs.readFile`非常有用。

您是否注意到其他可能对您不熟悉的新 JavaScript 结构？

# 帮助变量

在整本书中，您将看到`let`和`const`。这些是新的变量声明类型。与`var`不同，`let`是*块作用域*；它不适用于其包含的块之外：

```js
let foo = 'bar';

if(foo == 'bar') {
    let foo = 'baz';
    console.log(foo); // 1st
}
console.log(foo); // 2nd

// baz
// bar
// If we had used var instead of let:
// baz
// baz
```

对于永远不会改变的变量，请使用`const`，表示*constant*。这对编译器也很有帮助，因为如果变量保证永远不会改变，编译器可以更容易地进行优化。请注意，`const`仅适用于赋值，以下是非法的：

```js
const foo = 1;
foo = 2; // Error: assignment to a constant variable
```

但是，如果值是对象，`const`无法保护成员：

```js
const foo = { bar: 1 }
console.log(foo.bar) // 1
foo.bar = 2;
console.log(foo.bar) // 2
```

另一个强大的新功能是**解构**，它允许我们轻松地将数组的值分配给新变量：

`let [executable, absPath, target, ...message] = process.argv;`

解构允许您快速将数组映射到变量名。由于`process.argv`是一个数组，它始终包含 Node 可执行文件的路径和执行文件的路径作为前两个参数，我们可以通过执行`node script.js /some/file/path`将文件目标传递给上一个脚本，其中第三个参数分配给`target`变量。

也许我们还想通过这样的方式传递消息：

`node script.js /some/file/path This is a really great file!`

问题在于`This is a really great file!`是以空格分隔的，因此它将被分割成每个单词的数组，这不是我们想要的：

`[... , /some/file/path, This, is, a, really, great, file!]`

**剩余模式**在这里拯救了我们：最终参数`...message`将所有剩余的解构参数合并为一个数组，我们可以简单地`join(' ')`成一个字符串。这也适用于对象：

```js
let obj = {
    foo: 'foo!',
    bar: 'bar!',
    baz: 'baz!'
};

// assign keys to local variables with same names
let {foo, baz} = obj;

// Note that we "skipped" #bar
console.log(foo, baz); // foo! baz!
```

这种模式对于处理函数参数特别有用。在使用剩余参数之前，您可能会以这种方式获取函数参数：

```js
function (a, b) {
    // Grab any arguments after a & b and convert to proper Array
    let args = Array.prototype.slice.call(arguments, f.length);
}
```

以前是必要的，因为`arguments`对象不是真正的数组。除了相当笨拙外，这种方法还会触发像 V8 这样的编译器中的非优化。

现在，你可以这样做：

```js
function (a, b, ...args) {
    // #args is already an Array!
}
```

**展开模式**是反向的剩余模式——你可以将单个变量扩展为多个：

```js
const week = ['mon','tue','wed','thur','fri'];
const weekend = ['sat','sun'];

console.log([...week, ...weekend]); // ['mon','tue','wed','thur','fri','sat','sun']

week.push(...weekend);
console.log(week); // ['mon','tue','wed','thur','fri','sat','sun']
```

# 箭头函数

**箭头函数**允许你缩短函数声明，从`function() {}`到`简单 () => {}`。实际上，你可以替换一行代码：

`SomeEmitter.on('message', function(message) { console.log(message) });`

至于：

`SomeEmitter.on('message', message => console.log(message));`

在这里，我们失去了括号和大括号，更紧凑的代码按预期工作。

箭头函数的另一个重要特性是它们不会分配自己的`this`——箭头函数从调用位置继承`this`。例如，以下代码不起作用：

```js
function Counter() {
    this.count = 0;

    setInterval(function() {
        console.log(this.count++);
    }, 1000);
}

new Counter();
```

`setInterval`内的函数是在`setInterval`的上下文中调用的，而不是`Counter`对象的上下文，因此`this`没有任何与计数相关的引用。也就是说，在函数调用站点，`this`是一个`Timeout`对象，你可以通过在先前的代码中添加`console.log(this)`来检查自己。

使用箭头函数，`this`在定义的时候被分配。修复代码很容易：

```js
setInterval(() => { // arrow function to the rescue!
  console.log(this);
  console.log(this.count++);
}, 1000);
// Counter { count: 0 }
// 0
// Counter { count: 1 }
// 1
// ...
```

# 字符串操作

最后，你会在代码中看到很多反引号。这是新的**模板文字**语法，除其他功能外，它（终于！）使得在 JavaScript 中处理字符串变得更不容易出错和繁琐。你在示例中看到了如何轻松表达多行字符串（避免`'First line\n' + 'Next line\n'`这种构造）。字符串插值也得到了类似的改进：

```js
let name = 'Sandro';
console.log('My name is ' + name);
console.log(`My name is ${name}`);
// My name is Sandro
// My name is Sandro
```

这种替换在连接许多变量时特别有效，因为每个`${expression}`的内容都可以是任何 JavaScript 代码：

```js
console.log(`2 + 2 = ${2+2}`)  // 2 + 2 = 4
```

你也可以使用`repeat`来生成字符串：`'ha'.repeat(3) // hahaha`。

现在字符串是可迭代的。使用新的`for...of`结构，你可以逐个字符地拆分字符串：

```js
for(let c of 'Mastering Node.js') {
    console.log(c);
    // M
    // a
    // s
    // ...
}
```

或者，使用展开操作符：

```js
console.log([...'Mastering Node.js']);
// ['M', 'a', 's',...]
```

搜索也更容易。新的方法允许常见的子字符串查找而不需要太多仪式：

```js
let targ = 'The rain in Spain lies mostly on the plain';
console.log(targ.startsWith('The', 0)); // true
console.log(targ.startsWith('The', 1)); // false
console.log(targ.endsWith('plain')); // true
console.log(targ.includes('rain', 5)); // false
```

这些方法的第二个参数表示搜索偏移，默认为 0。`The`在位置 0 被找到，所以在第二种情况下从位置 1 开始搜索会失败。

很好，编写 JavaScript 程序变得更容易了。下一个问题是当程序在 V8 进程中执行时发生了什么？

# 进程对象

Node 的**process 对象**提供了有关当前运行进程的信息和控制。它是`EventEmitter`的一个实例，可以从任何范围访问，并公开非常有用的低级指针。考虑下面的程序：

```js
const size = process.argv[2];
const n = process.argv[3] || 100;
const buffers = [];
let i;
for (i = 0; i < n; i++) {
  buffers.push(Buffer.alloc(size));
  process.stdout.write(process.memoryUsage().heapTotal + "\n");
}
```

让 Node 使用类似这样的命令运行`process.js`：

```js
$ node process.js 1000000 100
```

程序从`process.argv`获取命令行参数，循环分配内存，并将内存使用情况报告回标准输出。你可以将输出流到另一个进程或文件，而不是记录回终端：

```js
$ node process.js 1000000 100 > output.txt
```

Node 进程通过构建单个执行堆栈开始，全局上下文形成堆栈的基础。这个堆栈上的函数在它们自己的本地上下文中执行（有时被称为**作用域**），这个本地上下文保持在全局上下文中。将函数的执行与函数运行的环境保持在一起的方式被称为**闭包**。因为 Node 是事件驱动的，任何给定的执行上下文都可以将运行线程提交给处理最终执行上下文。这就是回调函数的目的。

考虑下面的简单接口示意图，用于访问文件系统：

![](img/ed1fbee2-0820-44ff-96b6-1bacc2f11e1d.png)

如果我们实例化`Filesystem`并调用`readDir`，将创建一个嵌套的执行上下文结构：

```js
(global (fileSystem (readDir (anonymous function) ) ) )
```

在 Node 内部，一个名为`libuv`的 C 库创建和管理事件循环。它连接到可以产生事件的低级操作系统内核模式对象，例如定时器触发、接收数据的套接字、打开读取的文件和完成的子进程。它在仍有事件需要处理时循环，并调用与事件相关的回调。它在非常低的级别上进行操作，并且具有非常高效的架构。为 Node 编写的`libuv`现在是许多软件平台和语言的构建块。

与此同时，执行堆栈被引入到 Node 的单进程线程中。这个堆栈保留在内存中，直到`libuv`报告`fs.readdir`已经完成，此时注册的匿名回调触发，解析唯一的待处理执行上下文。由于没有进一步的事件待处理，也不再需要维护闭包，整个结构可以安全地被拆除（从匿名开始逆序），进程可以退出，释放任何分配的内存。构建和拆除单个堆栈的方法就是 Node 的事件循环最终所做的。

# REPL

Node 的**REPL**（**Read-Eval-Print-Loop**）代表了 Node 的 shell。要进入 shell 提示符，通过终端输入 Node 而不传递文件名：

```js
$ node
```

现在您可以访问正在运行的 Node 进程，并可以向该进程传递 JavaScript 命令。此外，如果输入一个表达式，REPL 将回显表达式的值。作为这一点的一个简单例子，您可以使用 REPL 作为一个口袋计算器：

```js
$ node
> 2+2
4
```

输入`2+2`表达式，Node 将回显表达式的值`4`。除了简单的数字文字之外，您可以使用这种行为来查询、设置和再次查询变量的值：

```js
> a
ReferenceError: a is not defined
 at repl:1:1
 at sigintHandlersWrap (vm.js:22:35)
 at sigintHandlersWrap (vm.js:96:12)
 at ContextifyScript.Script.runInThisContext (vm.js:21:12)
 at REPLServer.defaultEval (repl.js:346:29)
 at bound (domain.js:280:14)
 at REPLServer.runBound [as eval] (domain.js:293:12)
 at REPLServer.<anonymous> (repl.js:545:10)
 at emitOne (events.js:101:20)
 at REPLServer.emit (events.js:188:7)
> a = 7
7
> a
7
```

Node 的 REPL 是一个很好的地方，可以尝试、调试、测试或以其他方式玩耍 JavaScript 代码。

由于 REPL 是一个本地对象，程序也可以使用实例作为运行 JavaScript 的上下文。例如，在这里我们创建了自己的自定义函数`sayHello`，将其添加到 REPL 实例的上下文中，并启动 REPL，模拟 Node shell 提示符：

```js
require('repl').start("> ").context.sayHello = function() {
  return "Hello";
};
```

在提示符处输入`sayHello()`，函数将向标准输出发送`Hello`。

让我们把这一章学到的一切都应用到一个交互式的 REPL 中，允许我们在远程服务器上执行 JavaScript：

1.  创建两个文件`client.js`和`server.js`，并输入以下代码。

1.  在自己的终端窗口中运行每个程序，将两个窗口并排放在屏幕上：

```js
// File client.js
let net = require("net");
let sock = net.connect(8080);
process.stdin.pipe(sock);
sock.pipe(process.stdout);

// File server.js
let repl = require("repl")
let net = require("net")
net.createServer((socket) => {
  repl
  .start({
    prompt: "> ",
    input: socket,
    output: socket,
    terminal: true
  }).on('exit', () => {
    socket.end();
  })
}).listen(8080);
```

`client.js`程序通过`net.connect`创建一个新的套接字连接到端口`8080`，并将来自标准输入（您的终端）的任何数据通过该套接字传输。同样，从套接字到达的任何数据都被传输到标准输出（返回到您的终端）。通过这段代码，我们创建了一种方式，将终端输入通过套接字发送到端口`8080`，并监听套接字可能发送回来的任何数据。

另一个程序`server.js`结束了循环。这个程序使用`net.createServer`和`.listen`来创建和启动一个新的 TCP 服务器。代码传递给`net.createServer`的回调接收到绑定套接字的引用。在该回调的封闭内部，我们实例化一个新的 REPL 实例，给它一个漂亮的提示符（这里是`>`，但可以是任何字符串），指示它应该同时监听来自传递的套接字引用的输入，并广播输出，指示套接字数据应该被视为终端数据（具有特殊编码）。

现在我们可以在客户端终端中输入`console.log("hello")`，并看到显示`hello`。

要确认我们的 JavaScript 命令的执行发生在服务器实例中，可以在客户端输入`console.log(process.argv)`，服务器将显示一个包含当前进程路径的对象，即`server.js`。

只需几行代码，我们就创建了一种远程控制 Node 进程的方式。这是迈向多节点分析工具、远程内存管理、自动服务器管理等的第一步。

# 总结

有经验的开发人员都曾经面对过 Node 旨在解决的问题：

+   如何有效地为成千上万的同时客户提供服务

+   将网络应用程序扩展到单个服务器之外

+   防止 I/O 操作成为瓶颈

+   消除单点故障，从而确保可靠性

+   安全可预测地实现并行性

随着每一年的过去，我们看到协作应用程序和软件负责管理并发水平，这在几年前被认为是罕见的。管理并发，无论是在连接处理还是应用程序设计方面，都是构建可扩展架构的关键。

在本章中，我们概述了 Node 的设计者试图解决的关键问题，以及他们的解决方案如何使开发人员社区更容易创建可扩展、高并发的网络系统。我们看到了 JavaScript 被赋予了非常有用的新功能，它的事件模型得到了扩展，V8 可以配置以进一步定制 JavaScript 运行时。通过示例，我们学习了 Node 如何处理 I/O，如何编程 REPL，以及如何管理输入和输出到进程对象。

Node 将 JavaScript 转化为系统语言，创造了一个有用的时代错位，既可以脚本套接字，也可以按钮，并跨越了几十年的计算机演变学习。

Node 的设计恢复了 20 世纪 70 年代 Unix 原始开发人员发现的简单性的优点。有趣的是，计算机科学在这段时间内反对了这种哲学。C++和 Java 倾向于面向对象的设计模式、序列化的二进制数据格式、子类化而不是重写以及其他政策，这些政策导致代码库在最终在自身复杂性的重压下崩溃之前往往增长到一百万行或更多。

然后出现了网络。浏览器的“查看源代码”功能是一个温和的入口，它将数百万网络用户带入了新一代软件开发人员的行列。Brendan Eich 设计 JavaScript 时考虑到了这些新手潜在开发人员。很容易从编辑标签和更改样式开始，然后很快就能编写代码。与新兴初创公司的年轻员工交谈，现在他们是专业开发人员、工程师和计算机科学家，许多人会回忆起“查看源代码”是他们开始的方式。

回到 Node 的时间扭曲，JavaScript 在 Unix 的创始原则中找到了类似的设计和哲学。也许将计算机连接到互联网给聪明人带来了新的、更有趣的计算问题要解决。也许又出现了一代新的学生和初级员工，并再次反抗他们的导师。无论出于何种原因，小型、模块化和简单构成了今天的主导哲学，就像很早以前一样。

在未来几十年，计算技术会发生多少次变化，足以促使当时的设计师编写与几年前教授和接受为正确、完整和永久的软件和语言截然不同的新软件？正如*阿瑟·C·克拉克*所指出的，试图预测未来是一项令人沮丧和危险的职业。也许我们会看到计算机和代码的几次革命。另一方面，计算技术很可能很快就会进入一个稳定期，在这段时间内，计算机科学家将找到并确定最佳的范例来教授和使用。现在没有人知道编码的最佳方式，但也许很快我们会知道。如果是这样的话，那么现在这个时候，当创建和探索以找到这些答案是任何人的游戏时，是一个非常引人入胜的时刻，可以与计算机一起工作和玩耍。

我们展示 Node 如何以一种有原则的方式智能地构建应用程序的目标已经开始。在下一章中，我们将更深入地探讨异步编程，学习如何管理更复杂的事件链，并使用 Node 的模型开发更强大的程序。
