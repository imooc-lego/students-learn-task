# 全局安装@vue-cli时发生了什么
安装后，包会被下载在全局的node_modules里，然后解析`package.json`的`bin`，接着在安装node的主目录的bin文件下创建一个软链接，指向实际执行的文件