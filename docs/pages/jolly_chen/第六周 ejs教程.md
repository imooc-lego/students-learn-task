## `ejs` 用法

### 三种基本用法

参见代码

```js
const ejs = require('ejs');
const path = require('path');

const html = '<div><%=name%></div>';
const options = {};
const data = {
  name: 'jolly'
};

// 1. ejs.compile
const template = ejs.compile(html, options); // 可以反复利用，多次渲染时，效率较高
let compileTemplate = template(data);
console.log('template: ', compileTemplate);

// 2. ejs.render
data.name = 'john';
compileTemplate = ejs.render(html, data, options);
console.log('render: ', compileTemplate);

// 3. ejs.renderFile
// 3.1. Promise
data.name = 'Tom';
const templatePromise = ejs.renderFile(path.resolve(__dirname, 'template.html'), data, options);
templatePromise.then(compileTemplate => {
  console.log('templatePromise: ', compileTemplate);
});

// 3. ejs.renderFile
// 3.2. callback
data.name = 'Jim';
ejs.renderFile(path.resolve(__dirname, 'template.html'), data, options, (err, compileTemplate) => {
  console.log('renderFile: ', compileTemplate);
});
```

`template.html`

```ejs
<div><%=name%></div>
```

运行结果

```bash
PS D:\myProgram\templateCompile> node .\ejs.js
template:  <div>jolly</div>
render:  <div>john</div>
renderFile:  <div>Jim</div>
templatePromise:  <div>Tom</div>
```

### 标签含义

- 控制流脚本用 `<% %>`

  ```ejs
  <% if (user) { %> <!-- 有一个换行符 -->
  <div><%=user.name%></div> <!-- 有一个换行符 -->
  <% } %> <!-- 有一个换行符 -->
  ```

  - 输出时会有三个空行

- `<%_ %>` 删除其前面的空格。注意要有一个空格

- `<% _%>` 删除其后面的空格。注意要有一个空格

- `<%- %>` 输出非转义的数据到模板。场景一：直接输出html代码到模板

- `<%= %>` 输出数据到模板

- `<%# %>` 注释标签。不执行、不输出的内容

- `<%%` 输出字符串 '<%'

- `%%>` 输出字符串 '%>'

- `-%>` 删除紧随其后的换行符。将，一般结束符标签 `%>` 换为 `-%>` 可以去掉非预期的空行

### 辅助功能

#### 包含

```ejs
<%- include(path, data) -%>
```

引入另一个 `ejs` 模板，起到拷贝作用

- `path` 为模板路径
- `data` 模板需要的数据

#### 自定义分隔符

通过配置 `options` 中添加 `delimiter: '?'` 修改分隔符为 `?`。书写时，`%` 要变为 `?`

#### 自定义文件加载器

```js
const fs = require('fs');
const ejs = require('ejs');

let myFileLoader = function (filePath) {
  return 'myFileLoader: ' + fs.readFileSync(filePath).toString(); // 在所有加载的 ejs 模板前，加上 myFileLoader 字符串
};

ejs.fileLoader = myFileLoad; // 通过 myFileLoader 去加载文件
```



