## react

### 虚拟DOM
1. 将虚拟的新DOM和旧DOM使用diff算法对比差异
2. 得出差异DOM，然后对真实的DOM进行更新

### diff
1. tree diff 只对同一层级节点比较
2. component diff 比较组件类型
3. element diff 同一层级子节点通过id区分

### 优化
1. shouldComponentUpdate里控制子视图是否需要更新
2. 由于需要深复制props进行比较，使用immutableJs可以提升速度

### V16
1. Fiber，计算分片化，实现调度功能，异步渲染，不阻塞线程
2. createPortal
3. Render可以返还数组
4. 错误处理
5. 更小体积

### 高阶组件
1. 高阶组件可以看做是装饰器模式(Decorator Pattern)在React的实现。即允许向一个现有的对象添加新的功能，同时又不改变其结构，属于包装模式(Wrapper Pattern)的一种
2. 属性代理，引入里我们写的最简单的形式，就是属性代理(Props Proxy)的形式。
3. 应用，操作props，抽离state
4. 反向继承(Inheritance Inversion)，简称II，II采用通过 去继承WrappedComponent，本来是一种嵌套的关系，结果II返回的组件却继承了WrappedComponent，这看起来是一种反转的关系。通过继承WrappedComponent，除了一些静态方法，包括生命周期，state，各种function，我们都可以得到。
5. 应用，渲染劫持

### 组件是有分类的
1. 纯展示型的组件，数据进，DOM出，直观明了
2. 接入型组件，在React场景下的container component，这种组件会跟数据层的service打交道，会包含一些跟服务器或者说数据源打交道的逻辑，container会把数据向下传递给展示型组件
3. 交互型组件，典型的例子是对于表单组件的封装和加强，大部分的组件库都是以交互型组件为主，比如说Element UI，特点是有比较复杂的交互逻辑，但是是比较通用的逻辑，强调组件的复用
4. 功能型组件，以Vue的应用场景举例，路由的router-view组件、transition组件，本身并不渲染任何内容，是一个逻辑型的东西，作为一种扩展或者是抽象机制存在

## JS

### 函数式编程
1. 函数是一等公民
2. 数据不可变
3. 强制使用纯函数（输入输出没有副作用）
4. 循环递归调用
5. 函数只接收一个参数

### 闭包
1. 闭包就是能够读取其他函数内部变量的函数
2. 最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中

## 基础

### PWA
1. Service Workerss可以使PWA后台运行

## 网络与安全

### ajax
1. Web app的基础，实现异步与浏览器通信
2. 由于api不友好，现在有promise格式的fetch出现，但fetch不支持上传文件进度回调

### https
1. 获取服务器公钥和证书,
验证证书，生成浏览器私钥，
将请求和浏览器私钥加密发送给服务器,
服务端用服务器私钥解密请求,
用浏览器私钥加密返回数据给浏览器,
浏览器用浏览器私钥解密获得数据
2. 浏览器对称加密,服务器非对称加密

### XSS attack
1. XSS(Cross-site scripting)，跨站脚本攻击
2. 将脚本嵌入到目标页面，用户打开页面即被攻击，并可以获取用户cookie
3. React不会执行

### CSRF
1. cross-site request forgery， 跨域假冒请求
2. 通过跨域请求的方式获取用户cookie进行攻击
3. 防御策略，重要请求不使用GET方法