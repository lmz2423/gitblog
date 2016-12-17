<!--
author: 滇西之王
head: http://q.qlogo.cn/qqapp/100229475/C96DA226D9D07DECADAA54A78E9FEDF9/100
date: 2016-12-15
title: React动画实现
tags: reactjs
category: javascript
status: publish
summary: 本文介绍了ReactCSSTransitionGroup的使用
-->


[TOC]

## 动画组件
`ReactTransitionGroup`组件是个初级的动画附加组件。`ReactCSSTransitionGroup`是一个更高级的动画附加组件，它更容易实现基于css动画和css渐变动画。
## 更高级的API ReactCSSTransitionGroup
`ReactCSSTransitionGroup`是一个基于`ReactTransitionGroup`实现的动画组件。它更容易实现组件插入DOM或离开DOM树的动画。 
**导入动画组件**
```
import ReactCSSTransitionGroup from 'react-addons-css-transition-group' // ES6
var ReactCSSTransitionGroup = require('react-addons-css-transition-group') // ES5 with npm
var ReactCSSTransitionGroup = React.addons.CSSTransitionGroup; // ES5 with react-with-addons.js
```
**代码**
```
class TodoList extends React.Component {
   constructor(props) {
     super(props);
     this.state = {
       items:['hello','world','click','me']
     };
     this.handleAdd = this.handleAdd.bind(this).
   }

  handleAdd() {
   const newItems = this.state.items.concat([
     prompt('Enter some text');
   ]);
  }
  handleRomve(i) {
    let newItems = this.state.items.slice();
    newItems.splice(i, 1);
    this.setState({items: newItems});
  }

  render() {
    const items = this.state.items.map((item, i) => (
      <div key={item} onClick={() => this.handleRemove(i)}>
       {item}
      <div>
    ));

    return (
      <div>
       <button onClick = {this.handleAdd}> Add Item</button>
       <ReactCSSTransitionGroup
         transitionName ="example"
         transitionEnterTimeout= {500}
         transitionLeaveTimeout ={300}>
       </ReactCSSTransitionGroup>
      </div>
    );
  }
}
```
---
> **注意**
> 我们必须为`ReactCSSTransitionGroup`的所有孩子节点增加`key 属性` 即便是是只有几个孩子节点。只有增加之后，React才知道哪个元素是刚进入的，需要离开的，还是没有变化的。

在上面的这个组件中，当一个新的选项添加到`ReactCSSTransitionGroup`，该选项 将会添加`example-enter` 类。马上`example-enter-active`也会添加进来。
于是我们可以使用这些css类来触发一个css动画或过渡。
```
  .example-enter{
    opacity: 0.01;
  }
  .example-enter.example-enter-active{
    opacity:1;
    transition:opacity 500ms ease-in;
  }
  .example-leave {
    opacity:1;
  }
  .example-leave.example-leave-active{
   opacity:0.01;
   transition:opacity 300ms ease-in;
  }
```
---
> 注意
> `transitionEnter`和`transitionLeave`的默认值为`value` 我们必须设定`transitionEnterTimeout`和`transitionLeaveTimeout`的默认值。如果我们不需要进入和离开动画，只需要改成`transitionEnter={false}` 和`transitionLeave={false}`

## 自定义动画类
React也可以让我们在过渡阶段自定义类的名字。我们通过向`transitionName`传递一个对象。该对象可以包括`enter`,`leave`或包含`enter`,`leave`,`leave-active`,`leave`等属性。如果只提供`enter`和`leave`的类名，那么`enter-active`,`leave-active`的类名是在对应的`enter`和`leave`的类名上添加上`-active` 下面是对应的例子
```
// ...
<ReactCSSTransitionGroup
  transitionName={ {
    enter: 'enter',
    enterActive: 'enterActive',
    leave: 'leave',
    leaveActive: 'leaveActive',
    appear: 'appear',
    appearActive: 'appearActive'
  } }>
  {item}
</ReactCSSTransitionGroup>

<ReactCSSTransitionGroup
  transitionName={ {
    enter: 'enter',
    leave: 'leave',
    appear: 'appear'
  } }>
  {item2}
</ReactCSSTransitionGroup>
// ...
```
## 为一个元素增加动画
```
import ReactCSSTransitionGroup from 'react-addons-css-transition-group';

function ImageCarousel(props) {
  return (
    <div>
      <ReactCSSTransitionGroup
        transitionName="carousel"
        transitionEnterTimeout={300}
        transitionLeaveTimeout={300}>
        <img src={props.imageSrc} key={props.imageSrc} />
      </ReactCSSTransitionGroup>
    </div>
  );
}
```
## 禁止动画
我们可以禁止动画`enter`或 `leave`,例如我们只想要`enter`动画，并不想要`leave`动画，我们可以设置属性 `transitionEnter={false}`或`transitionLeave={flase}`来禁止对应的动画

> **注意**
> 使用`ReactCSSTransitionGroup` 无法编写逻辑复杂的动画，因为它没有对应的钩子，如果我们想精准的控制，我们需要`ReactTransitionGroup`来实现我们的目的