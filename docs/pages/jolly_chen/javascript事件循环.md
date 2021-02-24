## EventLoop

之所以称之为 **事件循环**，是因为它经常按照类似如下的方式来被实现：

```javascript
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

`queue.waitForMessage()` 会同步地等待消息到达(如果当前没有任何消息等待被处理)。

一个 EventLoop 中，可以有一个或多个任务队列。一个任务队列便是一系列有序任务(task)的集合；**每个任务都有一个任务源(task source)，源自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。**

`setTimeout`/`Promise`  等API便是任务源，而进入任务队列的是他们指定的具体执行任务。

[文章链接](https://www.cnblogs.com/zyl-Tara/p/10416886.html)

任务队列

- 宏任务 (macro)task

  (macro)task主要包含：script(整体代码)、`setTimeout`、`setInterval`、I/O、UI交互事件、`postMessage`、`MessageChannel`、`setImmediate`(Node.js 环境)

- 微任务 microtask

  microtask主要包含：`Promise.then`、`MutaionObserver`、`process.nextTick`(Node.js 环境)

