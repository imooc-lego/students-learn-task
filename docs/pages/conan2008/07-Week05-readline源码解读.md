### readline 简易实现
```javascript
function stepRead(callback) {
  /** 获取输入输出流 */
  const input = process.stdin;
  const output = process.stdout;

  /** 缓存用户输入 */
  let line = '';

  /** 监听用户输入， 并广播keypress事件， */
  emitKeypressEvents(input);

  /** 获取原始输入流，后续onKeypress单独处理 */
  input.setRawMode(true);
  input.resume();

  /** 接收用户输入的广播， 缓存每一次键盘输入line，处理特殊键盘事件 */
  input.on('keypress', function() {
    output.write(s)
    line += s;
    switch(s) {
      case '\r': 
        input.pause();
        callback(line);
        break;
    }
  });

}

function emitKeypressEvents(input) {
  /** 获取generator函数，执行到yield */
  const g = emitKeypress(input);
  g.next();

  /** 监听用户输入 */
  input.on('data', function (chunk) {
    g.next(chunk.toString());
  });
}

/** generator函数，while true配合 yield表达式 达到循环监听效果 */
function* emitKeypress(input) {
  while(true) {
    let ch = yield;
    input.emit('keypress', ch);
  }
}

stepRead(function(s) {
  console.log(`answer: ${s}`)
});
```
