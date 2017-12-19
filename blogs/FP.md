# JS函数式编程 数组部分风格

### 遍历数组:
```javascript
for(let item of array) {
    
}

// FP
// return array
array.map((item, index) => {})
```

### 查找数组中的一项:
```javascript
for(let item of array) {
    if(item === _item) {
        
    }
}

// FP
// return item 匹配条件的第一项
array.find(item => item === _item)
```

### 数组过滤:
```javascript
for(let item of array) {
    if(item > someValue) {
        
    }
}

// FP
// return array
array.filter(item => item > someValue)
```

## reduce
reduce() 方法接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始缩减，最终为一个值。
```javascript
array.reduce((preValue, currentValue, index, array) => {}, initialValue);

// 重点推荐
// fn(fn(fn(initialValue, array[0]), array[1]), array[2])
// 假如array = [1, 2, 3]
// 执行
array.reduce((preValue, currentValue) => preValue + currentValue, 0);
// 执行过程为
// step 1. return 0 + 1
// step 2. return 1 + 2
// step 3. return 3 + 3
```

### 求和：
```javascript
let array = [1, 2, 3, 4, 5];
let sum = 0;
for(let item of array) {
    sum += item;
}

// FP
let sum = array.reduce((pre, cur) => pre + cur, 0);
```

### 复杂应用：
```javascript
// 将'/aaa/bbb/ccc' 解析为 ['/aaa', '/aaa/bbb', '/aaa/bbb/ccc']
const fn = path =>
    path.split('/')
        .map(item => '/' + item)
        .reduce((pre, cur, index) => pre.concat(index < 1 ? '' : pre[index - 1] + cur), [])
        .filter((item, index) => index > 0);
```

