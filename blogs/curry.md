# JS手动实现柯里化(curry)函数

## 实现效果

```javascript
const curry_fn = curry(fn);
fn(1, 2, 3) == curry_fn(1)(2)(3);
```

## 实现思路
1. 通过闭包的方式储存传入参数
2. 通过函数的length属性获得参数个数
3. 当参数个数不够时直接返回方法
4. 存储的参数个数等于原函数参数个数时执行原函数

- 如果使用ES6参数默认值，length将不等于实际参数个数
- 参数由arguments获取，ES6直接使用rest参数实现

## 源码实现
```javascript
function curry(fn) {
    var length = fn.length; //获取原函数的参数个数
    var args = []; // args存储传入参数
    return function curryFn() {
        // 将arguments转换成数组
        var curryArgs = Array.prototype.slice.call(arguments); 
        args = args.concat(curryArgs);
        if (args.length > length) {
            throw new Error('arguments length error')
        }
        // 存储的参数个数等于原函数参数个数时执行原函数
        if (args.length === length) {
            return fn.apply(null, args);
        }
        // 否则继续返回函数
        return curryFn;
    };
}
```

## ES6版
```javascript
function curry(fn) {
    let length = fn.length;
    let args = [];
    return function curryFn(...curryArgs) {
        args = args.concat(curryArgs);
        if (args.length > length) {
            throw new Error('arguments length error')
        }
        if (args.length === length) {
            return fn(...args);
        }
        return curryFn;
    }
}
```

<a href="https://github.com/yjy5264/yjy5264.github.io"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>