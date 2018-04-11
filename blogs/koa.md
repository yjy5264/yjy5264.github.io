# koa源码解析
## 用法回顾

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx, next) => {
  fn1();
  await next();
  fn2();
});

app.use(async (ctx, next) => {
  fn3();
  await next();
  fn4()
});

app.use(async (ctx, next) => {
  fn5();
  await next();
  fn6()
});

app.listen(3000);
```

执行顺序 fn1 -> fn3 -> fn5 -> fn6 -> fn4 -> fn2
每当执行next时，执行下一个中间件，执行到最后一个中间件后开始往回执行
## 源码解析
### 源码执行步骤
1. 使用use方法即将middleware push 进koa中的this.middleware数组中
2. listen方法调用node的http.createServer和server.listen方法来创建服务，createServer的回掉执行下面的操作
3. 回掉首先执行compose(this.middleware)方法将middleware组合成一个promise对象来执行，这个promise对象即可完成中间件级联的操作
4. 将回掉传出的值req和res用于创建一个context对象，里面包含了request对象和response对象，这些对象提供了许多后台开发需要的参数和方法
5. 执行中间件
6. 得到结果给respond方法格式化数据
7. 或者捕获异常给onerror方法处理异常  

![](https://github.com/yjy5264/yjy5264.github.io/raw/master/images/koa.jpg)  

### compose
koa源码最重要的部分，如何实现中间件级联，以下是compose方法的源码(为方便观看，部分进行了ES6处理)

```javascript
function compose (middleware) {
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }

  return function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next // next = undefined
      if (!fn) return Promise.resolve() // 当运行到最后一个middleware时结束
      try {
        return Promise.resolve(
          fn(context, next = () => dispatch(i + 1))
        )
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```

1. compose方法使用递归的方式遍历每一个middleware
2. 遍历时将下一个middleware当作next传递给当前的middleware