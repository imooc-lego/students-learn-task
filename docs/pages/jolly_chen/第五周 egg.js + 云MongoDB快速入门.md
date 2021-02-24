### 为什么通过以下命令可以初始化 `egg.js` 项目

```bash
npm init egg --type=simple
```

这个有些像 "第三周 commander使用 -> `commander` 厉害的功能 ->  `program.command()` 的第二个参数：描述参数" 中讲到的功能。

这里实际是下载并执行 `create-egg` 脚手架，后面的参数将传入 `create-egg` 脚手架

### 真实域名和ip端口号的映射修改

- 修改host文件
- 使用 switchHosts 软件，管理host文件

### MongoDB 启动

```bash
mongod --dbpath=/Users/jolly/data/db
```

### `mongodb` 和 `mongoose` 

两者都可以帮助 `Node.js` 进行数据库操作，但 `mongoose` 是在 `mongodb` 基础上，再次封装的。