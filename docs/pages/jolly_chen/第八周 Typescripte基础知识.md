## 定义原始数据类型

- `null` 和 `undefined` 是所有类型的子类型。可以复制给任何类型

- ```typescript
  let arrOfNum: number[] = [1, 2, 3, 4]; // 定义元素类型为数子的数组
  ```

- `tuple` 元组类型

  ```typescript
  let user: [string, number] = ['jolly', 30]; // 两项，第一项类型为 string, 第二项类型为 number
  ```

- 函数声明

  ```typescript
  // 指定参数类型和返回值类型
  function add(x: number, y: number, z?: number): number{
    return x + y;
  }
  let add2 = (x: number, y: number, z?: number): number => {
    return x + y;
  }
  
  let add3: (x: number, y:number) => number = add2;
  ```

- Type inference 类型推断

  ```typescript
  let s = 'str'; // s 类型判定为 'string'. 不能被赋值为非 'string' 类型的值。
  ```

## interface

- 定义对象类型及形状

```typescript
interface Person {
  readonly id: number,
  name: string,
  age?: number
}
let jolly: Person = {
  name: 'jolly',
  age: 30,
  id: 1 // id不能被修改
}
```

- 定义函数类型

```typescript
interface Count {
  (x: number, y: number): number // 定义参数类型和返回值
}
const sum: Count = (x: number, y: number) => x + y;
```

- Indexable type 可索引类型

```typescript
interface Randmap {
  [propName: string]: string;
}
const map = {
  a: 'a',
  b: 'b',
  c: 'c'
  // ...
}
```

- LIke Array 类型

```typescript
interface LikeArray {
  [index: number]: string
}
const likeArray: LikeArray = ['a', 'b', 'c']; 
likeArray[0]; // 只能访问，没有其他方法
```

- duck typing

```typescript
// 定义一个四不像
interface FunWithProps {
  (x: number): number;
  name: string;
}
const a: FunWithProps = (x: number) => {
  return x
}
a.name = 'adf'
```

### 类和接口

- `public`
- `private`
  - 只在实例上可以访问，子类上不能访问
- `protected`
  - 只在子类上可以访问，实例上不能访问
- interface 约束类，implements 实现类

```typescript
interface ClockInterface {
  currentTime: number,
  alert(): void
}
interface GameInterface {
  play(): void
}

class Cellphone implements ClockInterface, GameInterface {
  currentTime: number = 123;
  alert() {

  }
  play() {
    
  }
}
```

### interface 约束构造函数

类的类型由两部分组成：

- 静态类型，这个类本身的类型
- 实例类型，使用 `new` 关键字创建的实例的类型

构造函数由约束静态类型，从而得到约束

```typescript
// 约束静态类型
interface ClockStatic {
  new (h: number, m: number): void;
  time: number
}
// 约束实例类型的属性方法
interface ClockInterface {
  currentTime: number,
  alert(): void
}
// 约束实例类型的属性方法
interface GameInterface {
  play(): void
}

const Cellphone: ClockStatic = class Cellphone implements ClockInterface, GameInterface {
  constructor(h: number, m1: number){
    
  }
  static time: 12
  currentTime: number = 123;
  alert() {

  }
  play() {

  }
}
```

## 泛型

### 函数和泛型

泛型解决的问题

- 类型推断不能延伸到函数

  - 泛型是在定义函数和接口的时候，不预先指定类型，而在使用时指定类型的一种特性

  ```typescript
  function echo<T>(arg: T): T {
    return arg; // T 是泛型的名称，可随意起名。可以理解为将来由参数类型替代
  }
  const result = echo('str'); // result 类型为 string。T = string
  
  // 泛型可以传入多个值
  function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]];
  }
  
  //
  ```

### 泛型和接口

```typescript
import { FunctionComponent } from 'react'
interface TestProps {
  title: string,
  desc: string
}

// 将接口 TestProps 传递到函数FunctionComponent中
const Test: FunctionComponent<TestProps> = (props) => {
  return (
    <>
    <h1>{props.title}</h1>
    <p>{props.desc}</p>
    </>
  )
}
```

- 泛型的默认值 `<T = {}>` 

- 类型别名: `type`

  ```typescript
  type PlusType = (x: number, y: number) => number
  let sum: PlusType = (x: numver, y: number) => x + y
  ```

- `Partial` 功能，接受一个泛型， 将其中的属性或函数变为可选。

  ```typescript
  interface Person {
    name: string,
    age: number
  }
  type PersonOptional = Partial<Person>
  /*PersonOption = interface {
    name?: string,
    age?: number
  }*/
  ```

  

#### 交叉类型 '&'

```typescript
interface IName {
  name: string
}
type IPerson = IName & { age: number }
let person: IPerson = { name: 'hello', age: 12 } // 同时要有两个接口中的定义的数据
```

#### 联合类型 '|'

```typescript
let numberOrString: number | string // numberOrString 为 number 或 string 类型
```

- **只能访问两种类型共有的属性和方法**

#### 类型断言

```typescript
function getLength(input: number | string) {
  const str = input as string
  if (str.length) {
    return str.length
  } else {
    const number = input as number
    return number.toString().length
  }
}
```

#### `Partial`  的实现

```typescript
interface CountryResp {
  name: string;
  area: number;
  population: number;
}
// keyof
type keys = keyof CountryResp
// keys = 'name' | 'area' | 'population' 联合字面量类型
// 在 keys 中的取值
type NameType = CountryResp['name']
type CountryOpt = {
  [p in Keys]?: CountryResp[p]
}
```

#### `extends` 

判断一个类型是否满足另一个类型的约束。

- 进行泛型约束

```typescript
interface IWithLength {
  length: number
}

function echoWithArr<T extends IWithLength>(arg: T): T {
  console.log(arg.length) // 将来传入的参数中，不一定有length。于是需要 extends 进行约束：传入的之中，必须有 length 属性
  return arg;
}
```

- 条件类型关键字

  ```typescript
  type NonType<T> = T extends null | undefined ? never : T // 假如泛型参数 T 为 null 或 undefined, 返回 never；否则返回 T
  ```

## 定义文件

用于 `ts` 编译时的检查，没有实现真正的代码功能

### 基础

- 定义文件命名：`xx.d.ts`

- `declare`

  ```typescript
  declare var JQuery: (selector: string) => any;
  ```

  如果 JQuery 是通过 `<script>` 标签引入，则以上可以是 `ts` 不报错

- npm 包名为 @types/xx 是声明文件

- `@type/XX` 包的创建，需要向 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 提交定义文件。[社区](https://www.typescriptlang.org/dt/search?search=)

- 默认情况下，node_modules 下面的 @types 包都会被编译器自动加载

### 高级用法

```bash
node_modules
  |_ @tayps
  		|_ myFetch
  				|_ index.d.ts
```

编写声明文件

```typescript
type HTTPMethod = 'GET' | 'POST' | 'PATCH' |'DELETE'
declare function myFetch<T = any>(url: string, method: HTTPMethod, data?: any): Promise<T>

declare namespace myFetch {
  const get: <T = any>(url: string) => Promise<T>;
  const post: <T = any>(url: string, data: any) => Promist<T>;
}

export = myFetch // 放入 node_modules 后要添加
```

## 知识点总结

- 基本类型
- interface
- class
- 泛型
- 声明文件
- 类型推论
- 联合类型
- 交叉类型
- 类型断言
- 内置类型
  - promise
  - partial ...
- 类型别名
- keyof, in

## 学习方法

多看别人的定义文件，尤其是大项目的。



