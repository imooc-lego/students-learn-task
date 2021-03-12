### require源码分析
require语句用来加载模块，返回该模块的exports对象，执行逻辑大致如下：
> + 首先查询缓存中是否存在该文件，如果有的话直接返回
> + 如果该文件是内部模块，查到后就直接返回；
> + 如果是node_modules模块，根据所在的父模块，确定该文件可能的安装目录。依次在每个目录中，查找该文件，找到后就直接返回；
> + 如果是本地文件，确定文件的决定路径，找到文件后返回;   
#####支持加载的文件类型：
> + .js类型
> + .json类型
> + .node类型
> + .mjs类型(node 15以下的版本不支持会直接抛出异常)
> + 其他文件类型(会统一转化为js类型)

#### 源码执行流程
1. 入口文件：`mod.require(path);`，调用module对象的require方法;
2. 调用`Module._load(id, this, /* isMain */ false);` 来对模块进行解析，第三个参数是判断是否为主文件
3.
 ```javascript
const filename = relativeResolveCache[relResolveCacheIdentifier];
    if (filename !== undefined) {
      const cachedModule = Module._cache[filename];
      if (cachedModule !== undefined) {
        updateChildren(parent, cachedModule, true);
        return cachedModule.exports;
      }
      delete relativeResolveCache[relResolveCacheIdentifier];
    }
``` 
判断该文件是否在缓存中，如果有的话直接返回module.exports对象  
.4. 寻找文件的真实路径：`const filename = Module._resolveFilename(request, parent, isMain);`,关键步骤如下:
> + 根据当前路径，查询所有可能的路径 `paths = Module._resolveLookupPaths(request, parent);` 如果该文件是node_modules
中的文件，会返回所有可能的路径:`[‌/Users/niechengyang/Desktop/ejs_test/node_modules,/Users/niechengyang/Desktop/node_modules,/Users/niechengyang/node_modules,/Users/node_modules,/node_modules,/Users/niechengyang/.node_modules,/Users/niechengyang/.node_libraries,/usr/local/lib/node]`，
如果是本地文件，会返回父模块所有的目录`parentDir = [path.dirname(parent.filename)]`比如：`['/Users/niechengyang/Desktop/ejs_test']`
> + 根据所有可能的路径去查找真实路径`const filename = Module._findPath(request, paths, isMain, false);`找到后返回文件真实路径
`/Users/niechengyang/Desktop/ejs_test/node_modules/ejs/lib/ejs.js`，并将该路径缓存起来；  

 
.5. 判断该模块是否为内置模块，如果是的话直接返回`const mod = loadNativeModule(filename, request, experimentalModules);`如果不是的话
生成一个新的Module对象来存储该文件`const module = new Module(filename, parent)`,并将该模块缓存下来`Module._cache[filename] = module;`， module对象属性如下：
> + id：源码文件路径，如：/Users/sam/Desktop/vue-test/imooc-test/bin/ejs/index.js
> + path：源码文件对应的文件夹，通过path.dirname(id) 生成
> + exports：模块输出的内容，默认为 {}
> + parent：父模块信息
> + filename：源码文件路径
> + loaded：是否已经加载完毕
> + children：子模块对象集合
> + paths：模块查询范围

.6. 加载模块`module.load(filename);`，详细过程如下：
> + 获取到文件的后缀名`const extension = findLongestRegisteredExtension(filename);`
> + 获取到执行该后缀名文件对应的函数`Module._extensions[extension](this, filename);` 
```javascript
Module._extensions['.js'] = function(module, filename) {
  if (filename.endsWith('.js')) {
    const pkg = readPackageScope(filename);
    // Function require shouldn't be used in ES modules.
    if (pkg && pkg.data && pkg.data.type === 'module') {
      const parentPath = module.parent && module.parent.filename;
      const packageJsonPath = path.resolve(pkg.path, 'package.json');
      throw new ERR_REQUIRE_ESM(filename, parentPath, packageJsonPath);
    }
  }
  const content = fs.readFileSync(filename, 'utf8');
  module._compile(content, filename);
```
> + 读取文件后，对文件进行编译`module._compile(content, filename);`将模板代码生成可执行的函数`const compiledWrapper = wrapSafe(filename, content, this);`
并且生成执行函数所需要的参数，包括 exports, require, module, filename, dirname
> + 将生成的函数执行起来`result = compiledWrapper.call(thisValue, exports, require, module, filename, dirname);`
> + 返回`module.exports`





