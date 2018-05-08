# 前端三大框架对比

## React与Vue

### 相同点
1. 使用 Virtual DOM，有较高的运行速度
2. 提供组件化功能
3. 可使用mobx与vuex进行状态管理，响应式、依赖追踪

### React
1. 子组件重复渲染问题需要手动优化
2. 可以使用redux进行状态管理，函数式、不可变、模式化，时间旅行
3. 可使用JSX，完全的javascript能力
4. 更繁荣的社区生态

### Vue
1. 可使用JSX，但推荐使用模版语言而不是JSX
2. 学习曲线平缓

## Angular

### 特点
1. 完善的MV*框架，包含模板，数据双向绑定，路由，模块化，服务，过滤器，依赖注入等所有功能
2. Typescript
3. 脏检查，对脏数据的检查就是脏检查，比较UI和后台的数据是否一致

### 比较
1. MVVM 列表渲染的初始化几乎一定比 Virtual DOM 慢，因为创建 ViewModel / scope 实例比起 Virtual DOM 来说要昂贵很多
2. 大而全大框架，学习成本高

<a href="https://github.com/yjy5264"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>
