# 开发文档

本项目基于 gitbook 开发

## 启动项目

- git clone 项目
- 安装插件 `npm install`
- 启动 `npm run dev`

## 手动构建

- 运行 `npm run build` （此处注意，新增文件时，`build` 之后看下效果，如果未生效，则再 `build` 一次）
- 结果输出到 `_book` 文件夹

## 修改 gitbook 配置

- 修改 `book.json`
- 运行 `npm run gitbook-install`
- 重新启动，或重新构建
- 提交代码

## 坑

- 图片文件夹之前叫 `_images` ，但是在 github pages 中图片不显示，返回 404 。改成 `images` 就好了。可能是 `_` 开头的文件夹，下面的文件就不公开。
