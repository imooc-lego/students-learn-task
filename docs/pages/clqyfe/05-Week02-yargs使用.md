# yargs 使用

```js
#!/usr/bin/env node

const dedent = require('dedent');
const yargs = require('yargs/yargs');
const { hideBin } = require('yargs/helpers');
const pkg = require('../package.json');
const context = {
    cliVersion: pkg.version
}

// console.log(hideBin(process.argv)) // [ '--help', '*', ... ]
// const argv = yargs(hideBin(process.argv)).argv;
const cli = yargs(hideBin(process.argv))

cli
    // 使用提示, $0: argv 中 $0，即脚手架软链接名称 zjw-cli-test
    .usage('Usage: $0 <command> [options]')
    // 输入不存在命令会给报误
    .strict() 
    // 最少需要一个 command 
    .demandCommand(1, 'A command is required. Pass --help to see all available commands and options.')
    // 当输入一个不存在的命令时会给出最接近的命令
    .recommendCommands()
    // 发生错误时执行的方法，不写 fail，输入错误指令会给出 --help 结果
    .fail((msg, err) => {
        console.log('msg: ', msg)
    })
    // 设置 help 别名为 h
    .alias('h', 'help')
    .alias('v', 'version')
    // 将容器宽度设置为终端的宽度 
    .wrap(cli.terminalWidth())
    // 在最后面输出的信息
    .epilogue(dedent`
        Your own log
    `)
    // 可配置 1 个以上的 option
    .options({
        'debug': {
            type: 'boolean',
            describe: 'Bootstrap debug mode',
            alias: 'd'
        }
    })
    // 配置单个 option
    .option('registry', {
        type: 'string',
        describe: 'Define global registry: npm, yarn',
        alias: 'r'
    })
    // 将 option 分组
    .group(['debug'], 'Debug Options:')
    .group(['r'], 'Repository Options:')
    // 注册命令
    .command(
        'init [name]',
        'do init project',
        // builder: 配置该命令专属 option
        yargs => {
            yargs.option('name', {
                type: 'string',
                describe: 'Name of project',
                alias: 'n'
            })
        },
        // handler: 处理命令的业务逻辑
        argv => {
            console.log(argv)
        }
    )
    // 注册命令的第二种方式
    .command({
        command: 'list',
        aliases: ['ll', 'la', 'ls'],
        describe: "List local packages",
        builder: yargs => {},
        handler: argv => {
            console.log(argv)
        }
    })
    // 解析参数
    .argv // 处理使用 .argv 的方式来完成参数解析外，还可以调用 yargs.parse 来完成命令行参数解析
    // .parse(argv, context) // hideBin(process.argv) 等价于 process.argv.slice(2)。parse 会将两个对象合并成一个新的对象作为命令行参数。
```
