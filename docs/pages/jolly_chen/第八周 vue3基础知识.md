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

### 为什么

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

### setup

不用像 vue2 那样，写 data, computed, methods 等分散的逻辑。而将这些写在 setup 方法中。`setup` 在props, data, computed, methods, 生命周期函数运行之前运行的，不能获得 this。

```js
import { defineComponent } from 'vue'
export default defineComponent ({
  setup() {
    return ;
  }
});
```

#### ref 生成响应式变量

```vue
<template>
	<h1>
    {{ count }}
  </h1>
	<a herf="javascript:;" @click="addCount">add</a>
</template>
<script>
import { defineComponent, ref } from 'vue'
export default defineComponent ({
  setup() {
    const count = ref(0);
    const addCount = () => {
      count.value++; // 使用引用类型，修改一个，其他也会更新
    }
    return {
      count,
      addCount
    };
  }
});
</script>
```

#### computed 计算属性

```vue
<template>
	<h1>{{ count }}</h1>
 	<p>{{ double }}</p>
	<a herf="javascript:;" @click="addCount">add</a>
</template>
<script>
import { defineComponent, ref, computed } from 'vue'
export default defineComponent ({
  setup() {
    const count = ref(0); // 返回 ref 对象
    const double = computed(() => count.value * 2); // double 是 computedRef 对象
    return {
      count,
      double
    };
  }
});
</script>
```

#### reactive 生成响应式对象

```vue
<template>
 	<p>Name: {{ person.name }}</p>
 	<p>Age: {{ person.age }}</p>
	<a herf="javascript:;" @click="person.change">change name</a>
</template>
<script>
import { defineComponent, reactive } from 'vue'
export default defineComponent ({
  setup() {
    const person = reactive({
      name: 'viking',
      age: 20,
      change() {
        person.name = 'maomao';
        person.age = 30;
      }
    })
    return {
      person
    };
  }
});
</script>
```

#### toRefs

接受 reactive 对象，返回新的对象，但对象的每一项属性，都变成了 ref 类型实例(响应式的)

```vue
<template>
 	<p>Name: {{ name }}</p>
 	<p>Age: {{ age }}</p>
	<a herf="javascript:;" @click="change">change name</a>
</template>
<script>
import { defineComponent, reactive, toRefs } from 'vue'
export default defineComponent ({
  setup() {
    const person = reactive({
      name: 'viking',
      age: 20,
      change() {
        person.name = 'maomao';
        person.age = 30;
      }
    });
    const person2 = toRefs(person);
    return {
      ...person2
    };
  }
});
</script>
```

### 生命周期

也是在 `setup` 中使用

`beforeCreate` 在 `setup` 之前运行，`created` 是在 `setup` 后运行，可以直接把 `beforeCreate` 和 `created` 中的逻辑，写在 `setup` 之中。所以这两个生命周期不再需要

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

## 副作用

### 纯函数

- 相同的输入，永远会得到相同的输出

- 没有副作用

  ```js
  const double = x => x*2; // 给定一个 x ，输出的值永远都是一样的
  Math.random(); // 不是一个纯函数
  ```

### 副作用

函数外部环境发生的交互

- 网络请求
- DOM 操作
- 订阅数据来源
- 写入文件系统
- 获取用户输入

### Vue 副作用处理

```js
import { defineComponent, watchEffect } from 'vue'
export default defineComponent {
  props: {
    msg: string
  }
  setup(props) {
    watchEffect(() => {
      console.log('props effect', props.msg);
    })
  }
}
```

- 组件第一次初始化时，会触发 `watchEffect`
- `watchEffect` 回调里面的值，没有发生变化，就不会触发副总用

### 深入 watchEffect 

- 自动收集依赖且触发

- 自动销毁 effect

  - 在 setup 或生命周期钩子函数中使用 `watchEffect` ，在组件销毁时，副作用也会一起销毁

  - 可以手动清除

    ```js
    const stop = watchEffect(() => {
      console.log('props effect', props.msg);
    });
    stop(); // 销毁
    ```

- 使副作用失效

  - `watchEffect` 中发送请求时，多次变化，势必多次请求

  - `watchEffect` 向回调中提供参数，以停止未完成的副作用

    ```js
    watchEffect((onInvalidate) => {
      console.log('props effect', props.msg);
      console.log('inner effect', count.value);
      const source = axios.CancelToken.source()
      axios.get(`https://jsonplaceholder.typicode.com/todos/${count.value}`, {
        cancelToken: source.token
      }).catch(err => {
        console.log(err.message);
      });
      onInvalidate(() => {
        source.cancel('trigger');
      });
    });
    ```

    

- 副作用执行顺序

  - `watchEffect` 都是异步执行的

  - `watchEffect` 先执行

  - `DOM updated` 再执行

    ```vue
    <template>
    	<h1 ref="node">{{msg}}</h1>
    	<h1>{{count}}</h1>
    	<a herf="javascript:;" @click="count++">change</a>
    </template>
    <script>
    import { defineComponent, ref } from 'vue'
    export default defineComponent ({
      props: {
        msg: string
      }
      setup() {
    		const node = ref<null | HTMLElement>(null);
        watchEffect(() => {
          const currentText = node.value ? node.value.innerText : '';
          console.log(currentText);
        }, {
          flush: 'post' // 默认是 pre：之前，
        })
        return {
          node
        };
      }
    });
    </script>
    ```

    - `watchEffect` 提供参数 `flush` 改变执行顺序。`post` 表示，在 `DOM updated` 之后再执行 `watchEffect`

  - React 的执行顺序不可以调整，都是在组件 updated 之后触发