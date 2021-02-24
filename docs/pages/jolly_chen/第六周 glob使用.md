# Glob

```bash
npm i -S glob
```

用来做文件过滤，过滤规则跟 `shell` 脚本中的一样。

glob 最早是出现在类Unix系统的命令行中, 是用来匹配文件路径的。比如，lib/**/*.js 匹配 lib 目录下所有的 js 文件。

### 匹配规则

不同语言的 glob 库支持的规则会略有不同。下面是 [node-glob](https://github.com/isaacs/node-glob) 的匹配规则。

- `*` 匹配任意 0 或多个任意字符
- `?` 匹配任意一个字符
- `[...]` 若字符在中括号中，则匹配。若以 `!` 或 `^` 开头，若字符不在中括号中，则匹配
- `!(pattern|pattern|pattern)` 不满足括号中的所有模式则匹配
- `?(pattern|pattern|pattern)` 满足 0 或 1 括号中的模式则匹配
- `+(pattern|pattern|pattern)` 满足 1 或 更多括号中的模式则匹配
- `*(a|b|c)` 满足 0 或 更多括号中的模式则匹配
- `@(pattern|pat*|pat?erN)` 满足 1 个括号中的模式则匹配

- `**` 跨路径匹配任意字符

链接：https://www.imooc.com/article/4053

### 举例

获取除 `node_modules`、`webpack.config.js` 之外的所有 `js` 文件。

```js
const glob = require('glob');

glob('**/*.js', {
  ignore: ['node_modules/**', 'webpack.config.js'] 
}, function(err, file) {
  console.log(err, file);
});
```

