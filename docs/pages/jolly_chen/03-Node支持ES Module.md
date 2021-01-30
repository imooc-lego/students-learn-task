## Node 支持 ES Module 的解决方案

### webpack

- 配置 `target: 'node'` 时，才会引入 `path` 等 `node` 内置库

- 垫片

  JS垫片就是，在低级环境中用高级语法时，在低级环境中手动实现该高级功能，模拟高级环境。形象的解释：垫片的作用就是，使四只脚不一样长的桌子变得平稳，为什么不平稳，因为每个脚的js语法版本不一样。

- `@babel/plugin-transform-runtime`  的作用是，提供“垫片”，例如 `regeneratorRuntime` 的“垫片” -- `regeneratorRuntime` 的实现。以下错误是因为缺少 `@babel/plugin-transform-runtime` 插件。

  ```bash
  webpack://Node_ES_Module/./core/index.js?:17
  _asyncToGenerator( /*#__PURE__*/regeneratorRuntime.mark(function _callee() {
                                  ^
  
  ReferenceError: regeneratorRuntime is not defined
  ```

  - `babel` 的配置

    还需要安装 `@babel/runtime-corejs3` 库

    ```javascript
    use: {
      loader: 'babel-loader',
        options: {
          presets: [ '@babel/preset-env' ],
            plugins: [
              ['@babel/plugin-transform-runtime',
               {
                 corejs: 3,
                 regenerator: true,
                 useESModules: true,
                 helpers: true
               }]
            ]
        }
    },
    ```

### Node 原生支持

- 将要执行的 `js` 文件和其依赖的 `js` 文件后缀改为 `mjs` 

- 使用以下命令执行

  ```bash
  node --experimental-modules index.mjs
  ```

- `node` 14 之后默认支持 ES Module，直接执行以下命令即可，不需要option `--experimental-modules` 

  ```bash
  node index.mjs
  ```

  