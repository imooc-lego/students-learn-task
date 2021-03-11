# B端项目需求分析和架构设计  
总体收获 - 数据驱动  
期待一下VUE3  

最近我在项目中通过JSON来渲染表单, 被提出不好维护  
提出不好维护后我有点想换思路，看到老师给的form-render觉得思路是可以的～  
代码类似:  
``` javascript  
 const originFormData = [
   {
      type: 'input',
      key: 'code', // 对应后端的字段
      title: '',
      rules: [],
      api: '', // 是否需要发送请求
      fetched: false, // 是否已经发送请求 一经发送修改状态 不管结果 防止发送多次请求
      dependency: 'otherKey', // 是否依赖其他字段
   }
 ]
```
我这遇到的痛点是  
- 输入框的数据是请求回来的  
- 输入框有些是级联的  

我现在渲染流程：  
现在还没和后端同学沟通返回的字段，所以我前端去拼数据结构  
状态管理是redux  
思路是 我通过Store(JSON)来渲染表单，表单的操作去修改Store, 触发渲染  
``` javascript  
const formData = initFormdata(originFormData); // 返回一个深度拷贝的对象 这样重置的时候方便一些
updataFormData(data) {
  // ... 业务代码
  // 我现在有递归去设置值 必要的时候会拷贝值
  dispatch(setFormData(data));
}
```  
级联的问题想通过订阅发布来做（还没具体实施）

所以请教一下老师 关于异步请求数据和级联 有没有好一点的解决思路。  
