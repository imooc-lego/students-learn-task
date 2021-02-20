## commander 库使用

基本代码

```javascript
#! /usr/bin/env node
const commander = require('commander');
const pkg = require('./package.json');

// 获取 commander 单例
// const { program } = commander;

// 实例化一个 Command 实例
const program = new commander.Command();

program
    .version(pkg.version)
    .parse(process.argv);  // 跟 yargs 不一样的参数传入
```

### 常用方法

- `program.usage('<command> [options]')` 说明脚手架命令的用法，不用像 `yargs` 一样获取命令名字 `$0` ，这个方法自动带上脚手架命令

- `program.name()` 指定命令的名字，会打印在`usage` 方法输出的信息前

- `program.option()` 定义全局 option。参数是 `option` 名字、描述、默认值。`yargs.option('optName', obj)` 

  ```javascript
  program.option('-d --debug', '是否开启调试模式', true); // 直接指定了简写和全称
  program.option('-e, --env <envName>', '获取环境变量') // 配置带参数的option
  ```

- `program.parse(process.argv)` 命令行参数解析

- `program.arguments()` 设置命令参数 

### 注册命令的两种方式

- `program.command()` 注册的是命令

  - 返回新的对象：命令对象

  - 参数说明：

    - 第一个参数可以配置命令名称及命令参数，参数支持**必选**（**尖括号表示**）、**可选**（**方括号表示**）及**变长**参数（**点号表示**，如果使用，只能是最后一个参数）

  - 基本用法

    ```javascript
    const clone = program.command('clone');
    clone
    	.description('clone a repository')
    	.action(() => {
    		console.log('do clone');
      });
    ```

- `program.addCommand()` 注册的是子命令。子命令下，又要注册新的命令。所以这个方法相当于分组

  ```javascript
  const service = new commander.Command('service'); // 子命令
  service
    .command('start [port]') // 返回命令对象，不再是 service
    .description('start service at some port')
    .action(port => {
      console.log('do service start', port);
    }); // 回调函数接收的参数：第一个到第n个是命令的参数，n+1位是option，最后是command命令本身
  program.addCommand(service);
  ```

- 两个命令帮助说明上，是有区别的

  - `clone -h`

    ```bash
    % comm-test clone -h
    Usage: comm-test clone [options] <source> [destination]
    
    clone a repository
    
    Options:
      -f, --force  是否强制克隆
      -h, --help   display help for command
    ```

  - `service -h`

    ```bash
    % comm-test service -h
    Usage: comm-test service [options] [command]
    
    Options:
      -h, --help      display help for command
    
    Commands:
      start [port]    start service at some port
      stop            stop service
      help [command]  display help for command
    ```

- 备注：`program.parse(process.argv)` 要放在添加命令逻辑之后

### `commander` 厉害的功能

**自动匹配所有输入的命令**

使用 `program.arguments()`

```javascript
program
  .arguments('<cmd> [options]') // 强制输入一个命令
  .description('test command', {
    cmd: 'command to run',
    options: 'options for command'
  })
  .action((cmd, options) => {
    console.log(cmd, options); // 对任何命令做出行为
  });
```

**`program.command()` 的第二个参数：描述参数**

当`.command()`带有描述参数时，就意味着使用独立的可执行文件作为子命令。`Commander` 将会尝试在入口脚本（例如 `./examples/pm`）的目录中搜索 `program-command` 形式的可执行文件，例如 `pm-install`, `pm-search `。通过配置选项（第三个参数） `executableFile` 可以自定义名字。可执行文件：`pm.js` `program-command.js`

```javascript
program
  .command('install [name]', 'install package') // 使命令 install 出现在 --help 信息中
  .alias('i')
```

输入 `comm-test i`

```bash
comm-test i s
/Users/jolly/Desktop/imooc/commander-test/node_modules/commander/index.js:925
        throw new Error(executableMissing);
        ^

Error: 'comm-test-install' does not exist
```

**`program.command()` 的第三个参数：配置选项**

多个脚手架之间的串行使用

```javascript
program
  .command('install [name]', 'install package', {
    executableFile: 'npm',
    isDefault: true,
  	hidden: true
  })
  .alias('i')
```

- `executableFile` 使得 `comm-test i` === `npm`，后面输入的 `option`，如果 `npm` 有就执行 `npm option`:  `comm-test i -v` === `npm -v`。如果`npm` 没有但当前脚手架有，就执行当前脚手架的 `option`。两者都没有，使用 `executableFile` 指定的命令报错，对于 `npm` 只是展示 `npm` 帮助信息，这是 `npm` 自己的逻辑决定的。

- `isDefaul: true` 表示输入的 `option` 没有指定是，默认执行 `executableFile` 指定的命令
- `hidden: true` 表示不再帮助信息中显示

### 高级定制

**自定义help信息**

```javascript
program.helpInformation = function() {
  // return 'your help infomation\n'; // 也可以在这里输出，不是很推荐
  return ''; // 先去掉原来的help
}
program.on('--help', () => {
  console.log('your help infomation');
})
```

**实现debug模式**

```javascript
program.on('option:debug', () => { // 这里，使用 --debug 没有效果
  if (program.opts().debug) { // 6.2.1 中可以用 program.debug 替换
    process.env.LOG_LEVEL = 'verbose';
  }
});
```

上述代码，会在命令执行前进行处理。

**对未知命令监听**

```javascript
program.on('command:*', (obj) => { // 未知命令都会命中这里的监听
  console.error('未知的命令：' + obj[0]);
  const availableCommands = program.commands.map(cmd => cmd.name());
  console.log('可用命令：' + availableCommands.join(','));
});
```

**注意：** 实现该功能，要注释掉 `program.arguments()` ，以及不让串行脚手架默认执行



