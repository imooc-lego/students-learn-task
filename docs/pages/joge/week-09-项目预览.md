最近项目中要处理不少状态 疯狂reset update init 中～

# Imooc CLI

通过脚手架建立项目模版

# ESLint

```jsx
module.exports = {
	extends: [
		// 可以拿其他公司的规范合集
	]
}
```

可以通过配置每次保存的时候自动ESLint

## 使用Prettier自动格式化代码

# 项目结构规范

react的项目推荐标准

按照路由或者功能来组织

- 避免多层嵌套
- 不要过度思考

命名选择: 帕斯卡规范

### 作业：文档

```tsx

// 文件夹一律小写
/components
-- ColorPicker.vue (组件使用Pascal命名方式)
/hooks
-- userURLLoader.ts (use开头，使用驼峰命名方式)
/plugins
-- hotKeys.ts (使用驼峰命名方式)
```

# Git Flow

### 不同分支的方式

release - 开发分支 测试结束后合如dev

hostfix - 线上BUG分支

master - 长期维护

dev - 长期维护

### pull request方式

PR - 适合持续交付、和大佬有交流

### 作业：文档

```tsx
分支名称
	新功能分支
		feature开头
	修改bug分支
		hotfix开头

提交时
	fix: [bug号] 修改什么什么问题 xxx
	

	docs:     只改动了文档相关的内容
	style:    不影响代码含义的改动，例如去掉空格、改变缩进、增删分号
	build:    构造工具的或者外部依赖的改动，例如webpack，npm
	refactor: 代码重构时使用
	revert:   执行git revert打印的message
```

# UI库 ant-design-vue

# vue-router

### 作业: hash模式

hash模式是获取url里的hash值，兼容性比较好，h5那个是通过H5的API来实现的。

基础都是通过监听事件来触发组件渲染。

理解router帮我们把URL里的参数改造成props通过props来判断当前需要渲染哪个组件。

# vuex

全局store创建和vue结合

通过钩子函数拿到全局状态

通过全局类型进行自动补全

通过module分割store

