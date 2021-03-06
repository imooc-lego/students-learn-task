### `readline` 内置模块

提供接口，一次一行地读取可读流（例如 `process.stdin` ）中的数据。基本使用：

```js
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('your name: ', (answer => {
  console.log(answer);

  rl.close();
}));
```

### `readline` 源码解读

- 强制将函数转为构造函数

  ```js
  if (!(this instanceof Interface)) {
    return new Interface(input, output, completer, terminal);
  }
  ```

- 获取事件驱动能力

  ```js
  EventEmitter.call(this);
  ```
  
- 监听键盘事件

  ```js
  emitKeypressEvents(input, this);
  
  // `input` usually refers to stdin
  input.on('keypress', onkeypress);
  input.on('end', ontermend);
  ```

#### `readline` 核心实现原理

![readline核心实现原理.png](./images/readline 核心实现原理.png)

- 核心 `emitKeypressEvents(input, this)`，监听终端中的键盘输入
  - `emitKeys()` 是一个 `Generator` 函数 
- 出现等待用户输入的核心： `stream.on('data', onData)`
- **`input.setRawMode(true)` 参数默认是 `false` 表示整行进行监听，变为 `true` 表示逐字监听，这时每次按下键盘的输入都必须处理。**
- 用户在命令行中输入，`_stream_readable.js` 中 `addChunk()` 函数派发事件 `stream.emit('data', chunk);`
- `this.input.pause()` 将输入流关闭

![readline核心实现原理脑图.png](./images/readline脑图.png)

#### 知识点

- `Number.isNaN(val)`
- `terminal = !!process.stdout.isTTY` / `!!process.stdin.isTTY` 判断是否终端

### 手写 `readline` 核心实现

```js
function stepRead(callback) {
  const input = process.stdin;
  const output = process.stdout;
  let line = '';

  function onKeypress(s) {
    output.write(s);
    line += s;
    switch(s) {
      case '\r': 
        input.pause();
        callback(line);
        break;
    }
  }

  emitKeypressEvents(input);
  input.on('keypress', onKeypress);

  input.setRawMode(true);
  input.resume();
}

function emitKeypressEvents(stream) {
  function onData(chunk) {
    g.next(chunk.toString());
  }
  const g = emitKeys(stream);
  g.next(); // 执行到第一个 yield 位置
  stream.on('data', onData);
}

function* emitKeys(stream) {
  while(true) {
    let ch = yield;
    stream.emit('keypress', ch);
  }
}

stepRead(function(s) {
  console.log('answer: ' + s);
});
```

