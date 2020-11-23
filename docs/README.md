# Web 前端架构师课 - 作业打卡

> 浅层学习看输入，深入学习看输出

汇总所有同学的：学习打卡，作业，学习笔记，分享

## 精选文章

- [【双越老师】我如何理解 Web 前端架构师 的角色和职责](./pages/👨‍🏫双越老师/01-我如何理解Web前端架构师的角色和职责.md)

## 查看其他同学的作业和笔记

页面左侧目录，就是所有同学的作业和学习笔记。

## 提交你的作业和笔记

注意，以下操作需要你了解 github 的 fork 和 pull request 机制。这也是多人协作开发所必备的技能。

### fork 源码

进入 https://github.com/imooc-lego/students-learn-task ，fork 项目到自己的 github 空间。

然后下载项目到本地，安装并启动。

```shell
cd students-learn-task
npm i
npm run dev # 访问 localhost:4000
```

## 写博客

作业、笔记，都可以用博客的形式。注意，**全程使用 markdown 语法**，不懂的自己去查。

- 新建 `docs/pages/<yourName>` ，`<yourName>` 即你在慕课网的用户名（或昵称、网名，都可以）
- 新建 `docs/pages/<yourName>/README.md` ，内容参考现有的 `docs/pages/双越老师/README.md`
- 在 `docs/pages/<yourName>` 下新建博客文件，命名格式按照 `01-xxx.md` `02-yyy.md` `03-zzz.md` ... 一定以序号 `01-` `02-` 开头！！ 
- 如果需要图片，可把图片文件放在 `docs/images` 中，然后在博客中引入



写完之后，提交代码到 github 。

## 提交 pull request

- 从你 fork 的仓库，提交 pull request 到 https://github.com/imooc-lego/students-learn-task ，**请求合并到 `main` 分支**
- 确定 https://github.com/imooc-lego/students-learn-task 有你提交的 pull request
- 去课程群里通知管理员，管理员会合并 pull request （除非你提交了违规内容）

## 自动发布

pull request 被合并之后，会触发 [github actions](https://github.com/imooc-lego/students-learn-task/actions) ，自动打包、发布到 https://imooc-lego.github.io 。

过程大概 3-5 分钟。
