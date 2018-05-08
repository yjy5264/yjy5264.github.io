# React高阶组件实现表单双向绑定

### 最终使用效果:
```javascript
import React from 'react';
import Item from './Item';
import { formCreate } from './formCreate';
 
@formCreate
export default class FromDemo extends React.Component {
    render() {
        return(
            <div>
                <Item fieldName='username' title='username' initialValue='username'>
                    <input />
                </Item>
                <Item fieldName='password' title='password' initialValue='password'>
                    <input />
                </Item>
                <div onClick={() => this.props.handleSubmit(fields => console.log(fields))}>handleSubmit</div>
            </div>
        )
    }
}
```

### 使用说明：
使用@formCreate（ES6修饰器语法）后, 直接将React受控组件（onChange & value 形式）如`<input />`放在`<Item>`里,无需再给`<input />`写入onChange和value props。
通过`this.props.handleSubmit()`获取form的值。

## 核心实现
### formCreate.js
```javascript
import React from 'react';

export const formCreate = WrappedComponent => class extends React.Component {
    state = { fields: {} };
    
    onChange = fieldName => value => this.setFieldValue(fieldName, value);
    
    handleSubmit = callback => callback(this.state.fields);
    
    getFieldValue = () => this.state.fields;
    
    setFieldValue = (fieldName, value) => this.setState(state => {
        state.fields[fieldName] = value;
        return state;
    });
    
    getField = fieldName => ({onChange: this.onChange(fieldName), value: this.state.fields[fieldName]});
    
    setInitialValue = (fieldName, value) => this.setFieldValue(fieldName, value);
    
    render() {
        const props = {
            ...this.props,
            handleSubmit: this.handleSubmit,
            getField: this.getField,
            getFieldValue: this.getFieldValue,
            setFieldValue: this.setFieldValue,
            setInitialValue: this.setInitialValue,
        };
        return <WrappedComponent {...props} ref={ref => this.instanceComponent = ref} />;
    }
};
```
### Item.js
```javascript
import React from 'react';
import { View, Text } from 'react-native';
 
export default class Item extends React.Component {
    form = this.props.children._owner.stateNode.props;
    
    componentDidMount() {
        this.updateInitialValue();
    }
    
    componentDidUpdate() {
        this.updateInitialValue();
    }
    
    updateInitialValue() {
        let { fieldName, initialValue } = this.props;
        (this.initialValue !== initialValue) &&
        (this.initialValue = initialValue) &&
        this.form.setInitialValue(fieldName, initialValue);
    }
    
    render() {
        let { title, fieldName } = this.props;
        return(
            <div>
                <div>{title}</div>
                {React.cloneElement(this.props.children, { ...this.form.getField(fieldName) })}
            </div>
        )
    }
}
```

### 说明
此处通过抽离state的方式将{onChange, value}以props.getField回调的形式传递给受控组件。
initialValue通过Item内部保存一个上一次的initialValue来判断是否变化。

<a href="https://github.com/yjy5264/yjy5264.github.io"><img style="position: absolute; top: 0; left: 0; border: 0;" src="https://github.com/yjy5264/yjy5264.github.io/raw/master/images/forkMe.png" alt="Fork me on GitHub"></a>
