# 范型

为什么会出现范型

```jsx
function echo(arg) {
	return arg;
}

const result = echo('str') // resulut: any
```

这样我们获取不到类型

所以范型是希望拿到类型 - 在使用中指定类型的特性

```jsx
function echo<T>(arg: T): T {
	return arg
}

const result = echo('str') // resulut: string
```

eg: 异步请求 通过不通的URL返回不同的数据

```jsx
function withAPI(url: string) {
	return fetch(url).then(res => res.json()):
}

withAPI('xxx').then(res => res); // res: any
```

```jsx
interface xxxRes {
	name: string;
	count: number;
}

interface yyyRes: {
	code: string;
	key: string;
}

function withAPI<T>(url: string): Promise<T> {
	return fetch(url).then(res => res.json()):
}

withAPI<xxxRes>('xxx').then(res => res); // res: xxxRes
withAPI<yyyRes>('xxx').then(res => res); // res: yyyRes
```

# interface

不用把JS中的数据结构和interface套在一起，interface另一个名称 duck typing～

描述一个函数

```jsx
interface RandomMap {
	[prpName: string]: string
}

const test: RandomMap {
	a: 'hello'
}
```

react中的函数组件的声明

```jsx
interface FunctionComponent<p = {}> { // <p = {}> 范型的默认值
	(props: PropsWithChildren<p>, context?:any): ReactElement | null;
	propsTypes?: WeekValidationMap<p>;
	defaultProps?: Partial<p>; // Partial内置类型 把传入的类型变成可选的
}
```

可以把interface理解成声明任何你想要的数据结构

## interface来约束类

```jsx
interface CLockInterface {
	cuttentTime: number;
	alert(): void;
}

interface GameInterface {
	play(): void
}

interface ClockStatic {
	new (h: number, m: number): void; // 函数类型
	time: number;
}

const Clock: ClockStatic = class Clock implements ClockInterface {
	constructor(h: number, m: number) {
	}
	static time = 123;
	currentTime: number = 123;
	alert() {
	}
}

class Clock implements ClockInterface, GameInterface {
	currentTime: number = 123;
	alert() {
	}
}
```

可以减少无用的继承，通过约束来使用

# 范型和接口

```jsx
const Test = (props) => {
	return (
		<>
			<h1>{props.title}</h1>
			<h1>{props.desc}</h1>
		<>
	)
}
```

交叉类型和联合类型 - 类型断言

```jsx
// 交叉类型
type PropsWithChildren<p> = p & { children ?: ReactNode };
```

```jsx
// 联合类型
let numberOrString: number | string

// 类型断言
xxx as number
```

partial:

```tsx
type Partial<T> = {
	[P in keyof T]?: T[p];
}

// keyof - 类比Object.keys(Obj)  是一个联合类型
// T[p] - 可以拿到类型值
```

### 作业：属性都为readonly

```tsx
type PartialReadOnly<T> = {
    readonly [p in keyof T]?: T[p]
}

interface IUser {
    phone: number,
    age: number
}

type optional = PartialReadOnly<IUser>;
```

## 范型约束

extends

# 定义文件

.d.ts - 声明文件

第三方库 一般都帮我们声明好了 命名一般以@type/xxxx开头

Definitely开源库

# VUE3

- 向后兼容
- 新特性
- breaking change
- 性能提升
- typescript

## Composition API

为什么需要

- 后期业务组件复杂后难以维护 - 把各个功能理解成函数，一个函数代表一个功能，方便调用和维护

```tsx
setup() {
	const count = ref(0) // ref - 定义成响应式数据
	const addCount = () => { count.value++; }

	return {
		count
	}
}
```

# 响应式编程

1. 保存effect
2. 监测值的改变 - proxy
3. 执行effect

# 纯函数和副作用

### 纯函数

1. 相同的输入，永远得到相同的输出
2. 没有副作用

### 副作用

函数外部环境发生的交互

1. 网络请求
2. DOM操作
3. 订阅数据来源
4. 写入文件系统
5. 获取用户输入

react - useEffect

vue - watchEffect 自动跟踪函数体内的值

### watchEffect

- 自动收集依赖并触发
- 自动销毁effect - watchEffect返回一个函数 执行后就销毁了
- 使副作用失效
- 副作用的执行顺序

### watch

# 自定义hooks

- 将相关的feature组合在一起
- 容易重用