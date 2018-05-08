# 如何用鼠标移动一个div(React实现)

### 鼠标事件
要用鼠标移动一个div首先要获取的是鼠标移动的事件。  
有三个事件是需要的
1. onMouseDown 鼠标按下触发
2. onMouseMove 鼠标移动触发
3. onMouseUp   鼠标按钮抬起触发

有了这三个事件，就可以获得鼠标完整的按下->移动->抬起完整的操作

### 移动距离
鼠标onMouseMove事件会得到很多的距离  
这些都不是需要的，真正需要的距离是鼠标移动的距离dx和dy  
那么我们选取onMouseMove返回的其中的一组clientX和clientY来处理  
1. 在class内存储一对变量lastX和lastY  
2. 第一次鼠标移动获取数据后将clientX和clientY放入lastX和lastY  
3. 第二次鼠标移动获取数据后，当前clientX与lastX的差即是dx，clientY与lastY的差是dy  
4. 并且再将clientX和clientY分别放入lastX和lastY，供下次移动使用  
5. 将dx和dy加上原来的坐标进行setState操作即可移动div  

### React实现源码
```javascript
import React from 'react'

export default class extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            translateX: 0,
            translateY: 0,
        };
        this.moving = false;
        this.lastX = null;
        this.lastY = null;
        window.onmouseup = e => this.onMouseUp(e);
        window.onmousemove = e => this.onMouseMove(e);
    }

    onMouseDown(e) {
        e.stopPropagation();
        this.moving = true;
    }

    onMouseUp() {
        this.moving = false;
        this.lastX = null;
        this.lastY = null;
    }

    onMouseMove(e) {
        this.moving && this.onMove(e);
    }

    onMove(e) {
        if(this.lastX && this.lastY) {
            let dx = e.clientX - this.lastX;
            let dy = e.clientY - this.lastY;
            this.setState({ translateX: this.state.translateX + dx, translateY: this.state.translateY + dy })
        }
        this.lastX = e.clientX;
        this.lastY = e.clientY;
    }

    render() {
        return (
            <div
                onMouseDown={e => this.onMouseDown(e)}
                style={　{transform: `translateX(${this.state.translateX}px)translateY(${this.state.translateY}px)`}　}
            >
                <div style={　{ width: 100, height: 100, backgroundColor: 'blue' }　} />
            </div>
        )
    }
};
```

### 注
1. onMouseDown事件由div提供，onMouseMove和onMouseUp事件由window提供的原因是防止鼠标移动过快造成事件丢失
2. this.moving为记录移动开始标志
3. stopPropagation作用为终止事件在传播过程的捕获、目标处理或起泡阶段进一步传播
4. div的实际移动由css3属性transform: { translateX, translateY }提供
5. 高阶组件封装的github项目链接[react-drag-hoc](https://github.com/yjy5264/react-drag-hoc)

<a href="https://github.com/yjy5264"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>
