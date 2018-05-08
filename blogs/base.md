# React发展几年前的现状个人理解(React新手向导)

### ES6
ES6带来的重要的改变我认为有三点：
1. promise/generator/async解决了回调地狱(callback hell)
2. => 箭头函数语法糖更好地解决了this指向问题，并简化λ表达式写法
3. class使JS进入面向对象编程(OOP)

### 方案一：MVC
既然有了OOP，那么new一个视图出来不就可以了  
![](https://github.com/yjy5264/yjy5264.github.io/raw/master/images/MVC.jpg)  
采用原来的MVC结构，Control控制视图，并且负责与Model层通信  
![](https://github.com/yjy5264/yjy5264.github.io/raw/master/images/MVCs.jpg)  
每一个页面都是一个MVC，页面之间都通讯用事件订阅就可以了  
在一个中大型项目中这是一个优雅的解决方案吗？  

### 方案二：状态机
将所有的视图写成v=f(state)的形式，所有的状态交给一颗state树来管理  
![](https://github.com/yjy5264/yjy5264.github.io/raw/master/images/stateTree.jpg)  
每一个输入事件作为一个action传给一个处理函数，这个函数会改变state树里的状态  
每一个状态发生变化都会引起该状态对应所有对应视图发生变化  
怎样写成v=f(state)的形式？  

### React
巧了，React其实就是一个状态机  
React组件内部改变this.state即会更新视图  
外部改变传入的props也会更新视图  

我们可以将组件大致分为两类：
1. display 显示组件，仅需要传入props值value即可
2. input 输入组件，在React里叫做受控组件，因为这种组件要传入两个props，一个是它的值value，另一个是value变化引起的回调onChange

(输入组件可以交给高阶组件封装的form表单自动处理，value和onChange都省了，可以参考我写的另一篇 [React高阶组件实现表单双向绑定](https://yjy5264.github.io/blogs/form))

将遇到的每一个模块封装成一个组件，内部紧耦合，外部解耦只需要传入状态  
将这些组件组装成一个完整的页面即是一个v=f(state)  
这个时候再将这些页面交给Redux来处理  

### Redux
严格的单向数据流是Redux架构的设计核心  
![](https://github.com/yjy5264/yjy5264.github.io/raw/master/images/redux.jpg)  
1. action事件传给reducer
2. reducer里编写纯函数处理action改变store里的state
3. store改变引起视图更新

### 总结
业务逻辑由redux处理，组件内部严格封装只留必要的接口  
如果组件有现成的(比如antd)，解耦完成的React方案开发起来速度简直不要太快  

<a href="https://github.com/yjy5264/yjy5264.github.io"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>
