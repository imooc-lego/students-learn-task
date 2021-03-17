## 新特性

- 向后兼容
- 新特性
- breaking change
- 性能提升
  - 大小 -41%
  - 初次渲染快 +51%
  - 更新 +133%
  - 内存减少 -54%
- typescript 支持

## Composition API

为什么

- 随着功能的增长，复杂组件代码难以维护

  - Vue2 api 通过一些列的 object 组织代码，缺少一种比较干净的在多个组件之间提取和复用的机制

    ```vue
    // vue2 的实现一个复杂功能，代码可能会很分散
    export default {
    	data {
    		// 复杂功能1的数据
    		// 复杂功能2的数据
    	},
    	methods: {
    		// 复杂功能1的实现方法
    		// 复杂功能2的实现方法
    	},
    	computed: {
    		// 复杂功能1
    		// 复杂功能2
      },
    	filters: {
    		// ...
      }
    }
    // 修改一个功能，可能就要在文件中修改 data, methods, computed, 甚至 filter 等
    ```

  - 使用 mixin 解决复用的问题

    - 不知道mixin暴露的对象中的数据是什么，方法是什么、返回是什么。对象是有一定封闭性的。
      - mixin中同样会有 data、computed、methods等，逻辑依然会分散
    - 命名冲突导致覆盖问题

- vue2 对 Typescript 的支持有局限

解决方法，使用函数

- 按逻辑组织代码，一个功能对应一个函数，代码组织更集中
- 在普通情况下，函数使用的参数、返回值更易查找。
- 在 ts 的加持下，函数参数和返回值的提示更友好。

## 深入响应式对象

1. 保存未来执行修改的代码（effect）
2. 监测值得改变
   1. 使用 `proxy` 对象实现
3. 值改变后，执行 trigger effect

### 监测值得改变

使用 `proxy` 拦截对目标对象的读取：

```js
const person = {
  name: 'Jone'
}
const handler = {
  // 目标对象，属性名，代理对象
  get(target, prop, receiver) {
    console.log('trigger get')
    return target[prop]
  }
  // 目标对象，属性名，要填入的值，代理对象
  set(target, prop, value, receiver){
    console.log('trigger set')
    traget[prop] = value
    return true  // 告诉设置成功
  }
}
const proxy = new Proxy(person, handler)
```

使用 `Reflect` 对象上的静态方法改写：

```js
const person = {
  name: 'Jone'
}
const handler = {
  get() {
    console.log('trigger get')
    return Reflect.get(...arguments) // Reflect.get() 接收的参数和 handler.get 接收的参数一样
  }
  set(){
    console.log('trigger set')
    return Reflect.set(...arguments) // Reflect.set() 接收的参数和 handler.set 接收的参数一样
  }
}
const proxy = new Proxy(person, handler)
```

上面两种方式，执行效果一样

### 存储和触发effect

- 将所有 effect 加入特定的数据结构
- 创建特定的函数可以再次运行这些 effect
- 使用 Proxy 的 getter 和 setter，将这些函数放入对应的位置

```js
let product = { price: 5, count: 2 }
let total = 0
let dep = new Set()
function track() {
  dep.add(effect)
}
function trigger() {
  dep.forEach(effect => effect()) 
}
const reactive = (obj) => {
  const handler = {
    get() {
      let result = Reflect.get(...arguments)
      track()
      return result
    },
    set() {
      let result = Reflect.set(...arguments)
      trigger()
      return result
    }
  }
  return new Proxy(obj, handler)
}
const product = reactive({ price: 5, count: 2 })
let effect = () => {
  total = product.price * product.count
}
console.log(total)
product.price = 10
console.log(`total is ${total}`)
```



