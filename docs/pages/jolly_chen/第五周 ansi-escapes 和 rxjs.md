## ANSI

`ansi-escapes` 全称 [`ANSI-escape-code`](https://handwiki.org/wiki/ANSI_escape_code) ANSI转义序列

ANSI escape sequences（ANSI转义序列）在终端中通过转义字符实现一些特殊操作的规范。特殊操作：光标上移、下移、左移，字体样式颜色改变等操作

## ANSI 规则使用

```js
console.log('\x1B[41m\x1B[4m%s\x1B[0m', 'your name:');
console.log('\x1B[2B%s', 'your name2:'); // 光标下移两行再输出
```

- `'\x1B'`
  -  `\x` 表示16进制
  - `1B` 是固定写法
- `41` 是在 [`ANSI-escape-code`](https://handwiki.org/wiki/ANSI_escape_code) 中 'Colors' 部分查到的代码。表示红色背景
- `\x1B[0m` 是将显示样式还原
- `4m` 表示下划线
- `m` 表示 `SGR`，设置显示属性，见 [`ANSI-escape-code`](https://handwiki.org/wiki/ANSI_escape_code) 中的 `Terminal output sequences`

## `rxjs`

响应式扩展库，实现异步。形式上跟 `promise` 相似

```js
const { range } = rxjs;
const { map, filter } = rxjs.operators;
 
const pipe = range(1, 200).pipe(
  filter(x => x % 2 === 1),
  map(x => x + x)
);
pipe.subscribe(x => console.log(x));
```

可以将 `pipe` 返回给用户，让用户实现自己想要的逻辑。