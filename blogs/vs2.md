# 前端三大框架对比(二)——数据更新

## Angular

### 最先说Angular的原因是因为它与其他两个框架不同，用的是脏检查机制(Dirty Checking)实现双向数据绑定

1. 在Angular中有一个Zone.js负责监听需要视图变化的事件触发
2. 每一个组件都都它自己的检测器(detector)，用于负责检查其自身模板上绑定的变量。
3. 将旧值跟新值进行比较，不相等就说明检测到变化，更新对应视图

## Vue & React 都是采用虚拟DOM来实现视图更新

### 虚拟DOM

vue和react的虚拟DOM的Diff算法大致相同：
1. tree diff 只对同一层级节点比较
2. component diff 比较组件类型
3. element diff 同一层级子节点通过id区分  

基于以上这三个约束，使得虚拟DOM的Diff算法的复杂度从O(n^3)降到了O(n)。

### Vue

1. Vue会遍历data对象的所有属性，并使用Object.defineProperty把这些属性全部转为getter/setter
2. 每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖
3. 当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新

### React

1. 当使用setState/forceUpdate，会调用render方法更新视图
2. 父组件更新视图时，会re-render子组件，所以看起来改变子组件的props也会更新视图

<a href="https://github.com/yjy5264/yjy5264.github.io"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>
