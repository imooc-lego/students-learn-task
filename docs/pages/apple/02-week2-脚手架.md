<!--
 * @LastEditors: qf
 * @Date: 2021-01-08 17:02:28
 * @Description: 
-->

# 脚手架

## 脚手架核心价值

- 自动化：项目重复代码拷贝/git操作/发布上线操作
- 标准化：项目创建/git flow/发布流程/回滚流程
- 数据化：研发过程系统化、数据化，研发过程可量化

## 组成

脚手架本质是一个操作系统的客户端

- 主命令：`vue`
- command：`create`
- command的param：`vue-test-app`

``` bash
vue create vue-test-app

// 强制覆盖某个已存在的文件夹
vue create vue-test-app --force

// 发布npm包
npm login
npm publish
```

## 脚手架本地link标准流程

### 链接本地脚手架

``` bash
cd your-cli-dir
npm link
```

### 链接本地库文件

``` bash
cd your-lib-dir
npm link
cd your-cli-dir
npm link your-lib
```

### 取消链接本地库文件

``` bash
cd your-lib-dir
npm unlink
cd your-cli-dir
# link存在
npm unlink your-lib
# link不存在
rm -rf node_modules
npm install -S your-lib
```

# Q&A

## windows一些常用命令

``` bash
# windows里cmd查看所有文件及目录命令
dir

# 返回上一级目录
cd ..

# 删除某个文件夹
# 如node_modules文件夹
rmdir /s/q node_modules
```

## 关于markdown

### vscode编写markdown使用到的插件

```bash
Markdown All in One
Markdown Preview Enhanced
markdownlint
Markdown Preview Mermaid Support
Markdown Preview Github Styling
```

### chrome浏览器如何预览markdown文档

- chrome浏览器的网址栏输入`chrome://extensions/`
- 应用商店安装`Markdown Viewer`
- 查看已经下载的扩展程序，然后找到Markdown Viewer
- 点击详细信息，将允许访问文件网址勾选。

# 感想

- 脚手架进大厂必备然鹅我不会，然鹅我不想进大厂
