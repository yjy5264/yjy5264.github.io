# 一道蚂蚁金服异步串行面试题

## 题目
```javascript
const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

const subFlow = createFlow([() => delay(1000).then(() => log("c"))]);

createFlow([
  () => log("a"),
  () => log("b"),
  subFlow,
  [() => delay(1000).then(() => log("d")), () => log("e")],
]).run(() => {
  console.log("done");
});

// 需要按照 a,b,延迟1秒,c,延迟1秒,d,e, done 的顺序打印
```
按照上面的测试用例，实现 createFlow：
1. flow 是指一系列 effects 组成的逻辑片段。
2. flow 支持嵌套。
3. effects 的执行只需要支持串行。

## 思路
1. 将effects里的array拉平
2. 因为effects里面有可能有flow，所以flow返回effects给上一层的使用
3. 使用async await顺序执行

## 实现
```javascript
const createFlow = (effects = []) => {
    const sources = effects.reduce((result, effect) => {
        if (typeof effect === "function") {
            return result.concat(effect);
        } else if (Array.isArray(effect)) {
            return result.concat(...effect);
        } else if (effect instanceof Object) {
            if (effect.sources) {
                return result.concat(...effect.sources);
            }
        }
        return result
    }, []);
    const run = async (callback) => {
        for (const task of sources) {
            await task();
        }
        callback && callback();
    };
    return {
        run,
        sources
    };
};
```