# 脚手架架构设计图

![filway-cli 脚手架架构设计图](/Users/zxxiaoxiao/Desktop/OpenSource/webqianduan/students-learn-task/docs/pages/filway/images/filway-cli 脚手架架构设计图.png)

# 脚手架准备过程

![脚手架准备过程](/Users/zxxiaoxiao/Desktop/OpenSource/webqianduan/students-learn-task/docs/pages/filway/images/脚手架准备过程.png)

```javascript
//检查版本号,直接加载package.json
// require支持加载的资源文件有: .js/.json/.node 3种类型的文件
// .js -> 必须要有module.exports/exports 必须输出模块
// .json -> 会执行JSON.parse
// .node -> C++ 插件 (process.dlopen)
// any(其它所有类型) -> 默认通过.js引擎来解析
const pkg = require('../package.json');

//检查node版本,不能低于 自定义常量LOWEST_NODE_VERSION
function checkNodeVersion() {
    const currentVersion = process.version;
    const lowestVersion = constant.LOWEST_NODE_VERSION;
    //比对版本号
    if (!semver.gte(currentVersion, lowestVersion)) {
        throw new Error(colors.red(`filway-cli 需要安装 v${lowestVersion} 以上版本的 Node.js`));
    }
}

//检查root启动
function checkRoot() {
    //root降级
    const rootCheck = require('root-check');
    rootCheck();
}

//检查用户主目录
const userHome = require('user-home');
function checkUserHome() {
    if (!userHome || !pathExists(userHome)) {
        throw new Error(colors.red('当前登录用户主目录不存在！'));
    }
}

//检查入参, 通过检查是否有debug参数, 来设置对应的log level
function checkInputArgs() {
    const minimist = require('minimist');
    args = minimist(process.argv.slice(2));
    checkArgs();
}
function checkArgs() {
    if (args.debug) {
        process.env.LOG_LEVEL = 'verbose';
    } else {
        process.env.LOG_LEVEL = 'info';
    }
    log.level = process.env.LOG_LEVEL;
}

//检查环境变量
function checkEnv() {
    const dotenv = require('dotenv');
    const dotenvPath = path.resolve(userHome, '.env');
    if (pathExists(dotenvPath)) {
        dotenv.config({
            path: dotenvPath
        });
    }

    createDefaultConfig();
}
function createDefaultConfig() {
    const cliConfig = {
        home: userHome,
    };
    if (process.env.CLI_HOME) {
        cliConfig['cliHome'] = path.join(userHome, process.env.CLI_HOME);
    } else {
        cliConfig['cliHome'] = path.join(userHome, constant.DEFAULT_CLI_HOME);
    }
    process.env.CLI_HOME_PATCH = cliConfig.cliHome;
}

//检查是否为最新版本,提示更新
//思路: 
// 1、通过npm api获取对应模块的所有高于当前版本号的版本号,然后取出最高的一个版本号(通过semver对比)
// 2、再通过对比上面取出的版本号和当前版本号, 如果有大于当前版本号 就提示更新
async function checkGlobalUpdate() {
    // 1、获取当前版本号和模块名
    const currentVersion = pkg.version;
    const npmName = pkg.name;
    // 2、调用npm API，获取所有版本号 https://registry.npmjs.org/xxxxx
    // 3、提取所有版本号，比对哪些版本号是大于当前版本号
    // 4、获取最新的版本号，提示用户更新到该版本
    const { getNpmSemverVersion } = require('@filway-cli/get-npm-info')
    const lastVersion = await getNpmSemverVersion(currentVersion, npmName)
    if (lastVersion && semver.gt(lastVersion, currentVersion)) {
        log.warn('更新提示',colors.yellow(`请手动更新 ${npmName}, 当前版本: ${currentVersion}, 最新版本: ${lastVersion} 
            更新命令: npm install -g ${npmName}`));
    }
  
}
```



# commander框架

```javascript
const { Command } = require('commander');
const pkg = require('../package.json');

const program = new Command();

program
  .name(Object.keys(pkg.bin)[0])
  .usage('<command> [options]')
  .version(pkg.version)
  .option('-d, --debug', '是否开启调试模式', false)
  .option('-e, --envName <envName>', '获取环境变量名称');

// command api 注册命令
const clone = program.command('clone <source> [destination]');
clone
  .description('clone a repository into a newly created directory')
  .option('-f, --force', '是否强制克隆')
  .action((source, destination, cmdObj) => {
    console.log('clone command called', source, destination, cmdObj.force);
  });



// addCommand 注册子命令
const service = new Command('service');

program.addCommand(service);

service
  .command('start [port]')
  .description('start service at some port')
  .action((port) => {
    console.log('do service start '+port)
  });

service
  .command('stop')
  .description('stop service')
  .action(() => {
    console.log('stop service')
  });

program
  .command('install [name]', 'install package', {
    executableFile: 'filway-cli',
    // isDefault: true,
    hidden: true
  })
  .alias('i')

// program
//   .arguments('<cmd> [options]')
//   .description('test command', {
//     cmd: 'command to run',
//     options: 'options for command'
//   })
//   .action( (cmd, options) => {
//     console.log(cmd, options);
//   })

// 高级定制1: 自定义help信息
// program.helpInformation = function () {return ''}; //清空原始的help信息
// program.on('--help', function () {
//   console.log('my help info')
// })

//高级定制2: 实现debug模式
program.on('option:debug', function () {
  if (program.opts().debug) {
    process.env.LOG_LEVEL = 'verbose'
  }
  console.log(process.env.LOG_LEVEL)
})

//高级定制3: 未知命令的监听
program.on('command:*', function (obj) {
  console.log(obj);
  console.error('未知的命令: ' + obj[0]);
  const availableCommands = program.commands.map( cmd => cmd.name());
  //console.log(availableCommands);
  console.log('可用命令: '+availableCommands.join(','));
})

program
  .parse(process.argv);
```

# 如何让Node支持ES Module

- 通过webpack + babel 编译
- node js原生支持, 需要.mjs后缀

