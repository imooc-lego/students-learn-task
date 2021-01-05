# lerna源码分析

## lerna实现原理

1. 通过 import-local 库优先调用本地的 lerna 命令。巧妙的利用异步的方式先输出提示信息，再完成命令的执行。
2. 通过 yargs 生成脚手架，先注册全局属性，在注册命令，最后通过 parse 方法解析命令行参数。
3. 注册命令时需要传入 builder 和 handler 两个方法。builder 用来配置命令的专属 option，handler 用来处理命令的业务逻辑，同时命令行参数作为函数参数方式传入函数。
4. lerna 通过其自有的方式来引用本地依赖，而不是通过 npm link 的方式。具体写法：在 package.json 的依赖项中写入，在执行 lerna publish 时会自动将该依赖替换。

```json
// package.json
{
    "dependencies": {
        "lerna": "file:core/lerna"
    }
}
```

## import-local 源码分析

TODO: 学完脚手架内容再来啃这部分内容

## node 的 module 模块分析

TODO: 学完脚手架内容再来啃这部分内容

## vscode 源码调试技巧

[vscode 调试技巧](https://www.yuque.com/docs/share/faa9343a-42c7-4493-b2a7-aafd8e369005?#)
