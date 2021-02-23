# init阶段  
通过之前的命令进入到init的npm包 业务逻辑都在这个init包里处理  
## 准备阶段  
这个阶段需要拿到开发者填写的项目模板信息  
步骤:  
1. 当前目录是否为空(通过交互或者命令行里的force字段来控制是否清空目录)  
2. 上面逻辑处理完 进入交互阶段获取到项目信息(requirer的链式调用，名称和版本号的校验)  
3. 项目类型是否为组件  
![准备阶段](./images/week5-1.png "准备阶段")  

# 学习过程中遇到的问题  
### 获取命令参数的问题  
```javascript  
    // 老师通过this._cmd.force拿到信息
    this.force = !!this._cmd.force
    // 我的版本需要_cmd[1]
    this.force = !!this._cmd[1].force
```  