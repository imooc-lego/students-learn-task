<br/>

# 产出
组件库地址：[lego-editor-components](https://www.npmjs.com/package/lego-editor-components)

Travis CI/CD：[CI/CD](https://travis-ci.com/github/llf137224350/lego-editor-components)


## 踩坑一
> 使用npm run build进行打包时，打包出的文件如xxx.esm.js包含defaultProps.ts中定义的数据，但是并没有导出，如`textDefaultProps`、`imageDefaultProps`、`shapeDefaultProps`等。导致组件使用方无法获取到这部分数据。

**解决办法**

src下index.ts中增加如下代码

```javascript
export * from './defaultProps';
```

## 踩坑二
默认情况下浏览器会报如下错误且l-text无法正常渲染：
```shell
resolveComponent can only be used in render() or setup()
```
导致原因为组件库中vue与editor中vue不一致导致，在4-6小节有讲，需要使用npm link让vue指向editor中的vue，命令如下
```shell
npm link editor项目所在目录/node_modules/vue
```
<br/>

# Travis CI/CI踩坑记

## 踩坑一

> 运行`travis encrypt --pro npm_token --add deploy.api_key`命令会提示`not logged in, please run travis login --pro`，意思是需要先执行`travis login --pro`完成登录，但在执行`travis login --pro`输入github用户名和密码时会报如下错误：
```shell
We need your GitHub login to identify you.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.

Try running with --github-token or --auto if you don't want to enter your password anyway.

Username: github_username
Password for github_username: ****************
Not Found
for a full error report, run travis report --pro
```
**解决办法**
使用`travis login --pro --github-token your_token`完成登录，your_token在自己github中配置获取。

## 踩坑二
> 使用tag时，让travis在测试通过检测到有tag时自动publish包到npm，其中.travis.yml配置如下

```shell
language: node_js
node_js:
  - node

deploy:
  provider: npm
  email: xxx@qq.com
  skip_cleanup: false
  api_key:
    secure: FbmjqqmwYNJ/amKnuSdQsNW9U6zy2KxCRAaCzO11mvpHkSklgJ+Dfa/E6bOAfY7js1v723YI9wsUELTJyWZJsrwGMCh8cJ/r7rgm9PdYQ7ZBrWV2HStp+XtMA75yVMMiQyAHg7S8i/fDvwatEPqieFFPr9fxp2Sr7UkyJBudffFfUStk+lUgfwChsHYGJpNbgOu/b8ilxoXAQsy05ojgTubXr4uAIFsggyFcQjvJeFgpWvTlcaCODpf+PDD02wx8KtR/P/vcyhat8GnHz4ypk0VOylsYLqTlNqCBIXvKx55zmSQEmnmHnowiHgSYmZxh4kNZA/00feO//RMKJoPd3Ry/ILqkR4Fn/agbGkW8HpaWCn43G70CI9iqh+umsWD8iE9tknLABpUXPBqnIdOiZJEUAa7FyO0AOoxeNZiRLC1k7T8fKIsf9xxs8FwWtrXc7oCttND63uJfldGDl+KB8B75IHiDp96PIaJsxaUl+PKelFBNa+TBSc+UHAup7FNP+4IiyLQP4yztqkA8gBFUC1EpHdGDXEsHibn8isOL7BOWyjgVJIMNXdSNeS97zJaKpGN3hVSoGIFYDj7gX2NmzodxR+44e7kinaWDy4RuMweOx3hVTc1yW1D+2ruAAGztQaZcUmZsRwcGzcRXfPZUa6R5isA/q3dcnM7pwTYGfMI=
  on:
    tags: true
```
travis运行vue-cli-service test:unit测试通过，执行到Deploying application时提示如下错误
```shell
sh: 1: vue-cli-service: not found
npm ERR! code 127
npm ERR! path /home/travis/build/github_username/lego-editor-components
npm ERR! command failed
npm ERR! command sh -c vue-cli-service lint --max-warnings 5
npm ERR! A complete log of this run can be found in:
npm ERR!     /home/travis/.npm/_logs/2021-03-01T12_47_43_636Z-debug.log
npm ERR! code 127
npm ERR! path /home/travis/build/github_username/lego-editor-components
npm ERR! command failed
npm ERR! command sh -c npm run lint && npm run test && npm run build
npm ERR! A complete log of this run can be found in:
npm ERR!     /home/travis/.npm/_logs/2021-03-01T12_47_43_742Z-debug.log
```
**解决办法**
.travis.yml中skip_cleanup配置为false导致，需要配置为true。

**完整配置如下：**
```shell
language: node_js
node_js:
  - node

deploy:
  provider: npm
  email: xxx@qq.com
  skip_cleanup: true
  api_key:
    secure: FbmjqqmwYNJ/amKnuSdQsNW9U6zy2KxCRAaCzO11mvpHkSklgJ+Dfa/E6bOAfY7js1v723YI9wsUELTJyWZJsrwGMCh8cJ/r7rgm9PdYQ7ZBrWV2HStp+XtMA75yVMMiQyAHg7S8i/fDvwatEPqieFFPr9fxp2Sr7UkyJBudffFfUStk+lUgfwChsHYGJpNbgOu/b8ilxoXAQsy05ojgTubXr4uAIFsggyFcQjvJeFgpWvTlcaCODpf+PDD02wx8KtR/P/vcyhat8GnHz4ypk0VOylsYLqTlNqCBIXvKx55zmSQEmnmHnowiHgSYmZxh4kNZA/00feO//RMKJoPd3Ry/ILqkR4Fn/agbGkW8HpaWCn43G70CI9iqh+umsWD8iE9tknLABpUXPBqnIdOiZJEUAa7FyO0AOoxeNZiRLC1k7T8fKIsf9xxs8FwWtrXc7oCttND63uJfldGDl+KB8B75IHiDp96PIaJsxaUl+PKelFBNa+TBSc+UHAup7FNP+4IiyLQP4yztqkA8gBFUC1EpHdGDXEsHibn8isOL7BOWyjgVJIMNXdSNeS97zJaKpGN3hVSoGIFYDj7gX2NmzodxR+44e7kinaWDy4RuMweOx3hVTc1yW1D+2ruAAGztQaZcUmZsRwcGzcRXfPZUa6R5isA/q3dcnM7pwTYGfMI=
  on:
    tags: true
```

## 总结
> 13周看着难度不大，实践起来坑却不少！！！！
