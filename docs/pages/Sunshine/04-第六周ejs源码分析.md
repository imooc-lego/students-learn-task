### ejs源码分析
ejs是高效的嵌入式 JavaScript 模板引擎，可以在运行时将模板字符串替换成用户传入的值;    
以代码```let template = ejs.compile(str, options);
      template(data);```为例，分析整个ejs源码执行流程；   
参数解析：   
str：传入的模板字符串；  
options： 参数对象，主要包括如下参数：   
* cache 缓存编译后的函数，需要指定 filename   
* filename 被 cache 参数用做键值，同时也用于 include 语句
* context 函数执行时的上下文环境
* compileDebug 当值为 false 时不编译调试语句
* client 返回独立的编译后的函数
* delimiter 放在角括号中的字符，用于标记标签的开与闭
* debug 将生成的函数体输出
* _with 是否使用 with() {} 结构。如果值为 false，所有局部数据将存储在 locals 对象上。
* localsName 如果不使用 with ，localsName 将作为存储局部变量的对象的名称。默认名称是 locals
* rmWhitespace 删除所有可安全删除的空白字符，包括开始与结尾处的空格。对于所有标签来说，它提供了一个更安全版本的 -%> 标签（在一行的中间并不会剔除标签后面的换行符)。
* escape 为 <%= 结构设置对应的转义（escape）函数。它被用于输出结果以及在生成的客户端函数中通过 .toString() 输出。(默认转义 XML)。
* outputFunctionName 设置为代表函数名的字符串（例如 'echo' 或 'print'）时，将输出脚本标签之间应该输出的内容。
* async 当值为 true 时，EJS 将使用异步函数进行渲染。（依赖于 JS 运行环境对 async/await 是否支持）   
data: 传入的真实数据，替换模板字符串中的占位内容；   

#### 源码执行流程
1. templ = new Template(template, opts); 生成一个Template对象，在这个过程中主要执行如下操作：   
> + 对传入的options进行处理，比如delimiter 是否是用户自定义的还是默认的；
> + 生成一个正则表达式，主要是用作在编译时对模板字符串进行解析；默认情况下返回值是：`/(<%%|%%>|<%=|<%-|<%_|<%#|<%|%>|-%>|_%>)/`

2. 对生成的Template对象templ进行编译并将结果返回：`templ.compile()`,执行如下操作：
> + 执行`this.generateSource()`函数，核心是为了解析模板字符串`this.parseTemplateText()`；该方法的核心是用之前生成的正则表达式循环调用`exec`方法去得到一个数组返回
```javascript
    var result = pat.exec(str);
    var arr = [];
    var firstPos;

    while (result) {
      firstPos = result.index;

      if (firstPos !== 0) {
        arr.push(str.substring(0, firstPos));
        str = str.slice(firstPos);
      }

      arr.push(result[0]);
      str = str.slice(result[0].length);
      result = pat.exec(str);
    }
    if (str) {
          arr.push(str);
    }
    return arr
```
比如将模板字符串`<% -%><%_ %><div><%= user.name %></div>` 转为数组`[‌<%, ,-%>,<%_, ,%>,<div>,<%=, user.name ,%>,</div>]`
> + 执行`self.scanLine(line);`将数组中的每个元素传入。经过一系列的规则匹配后，生成字符串；再根据条件加上一些prepend字符串后生成一个函数字符串如下：
```var __output = "";
   function __append(s) { if (s !== undefined && s !== null) __output += s }
   with (locals || {}) {
     ;  
     ;  
     ; __append("<div>")
     ; __append(escapeFn( user.name ))
     ; __append("</div>")
   }
   return __output;
   ```
   最后用Function函数将字符串变为可执行的函数`fn = new Function(opts.localsName + ', escapeFn, include, rethrow', src);`返回

3. 最后执行该函数`template(data)`，传入data数据就可以得到结果`<div>sunshine</div>`


