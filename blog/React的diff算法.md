<!--
author: 滇西之王
head: http://q.qlogo.cn/qqapp/100229475/C96DA226D9D07DECADAA54A78E9FEDF9/100
date: 2016-12-11
title: ReactJs的diff算法
tags: ReactJs
category: javascript
status: publish
summary: 大致讲了ReactJs的diff算法
-->
## Reactjs Diff算法
React 提供了特别直观的API接口，所以我们不必要担心每次数据更新时，发生了什么。我们写程序的时候，变得更容易，但是我们不知道React是怎么实现的。
下面的这篇文章解释了React的diff算法，该算法能使React 知道组件的更新是可预测的，并且效率很高。
## 动机
当我们使用React的时候，我们很容易想到`render()`函数是是用来创建React元素树。当`state`或`props`更新的时候。`render`函数将会返回一个不同的React元素树。
变更React元素树，操作次数最小，是算法的关键。在《算法的艺术中》对更新一颗树的算法时间复杂度是`O(n^3) ` n代表树上的节点数。
如果我们使用常规算法去更新`React元素树`,如果该树有1000元素，那么我们要比较10亿次。代价太昂贵了。因此，React基于两点假设，实现了一个时间复杂度为O(n)的算法
1. 两个不同类型的元素将会产生不同的树。
2. 开发者可以通过`key`来表明该孩子元素没有发生变化。

## diff 算法
当比较两颗树的不同点的时候，React首先比较两颗树的根元素。如果根元素的类型不同，那这两颗树就是不同的。

## 不同类型的元素
**只要根元素类型不同**，React就会卸载老树，从头开始构建新树。假设我们从`<a>`变更到`<img>`,或从`<Article>`变更到`<Comment>`,或从`<Button>`变更到`<div>` 这都会导致重建整个React树。
当卸载老树的时候，旧的的DOM节点被删掉，对应的组件实例将会调用`componentWillUnmount()`方法。当构建一颗新树，新树被插入DOM中，新树的组件实例会调用`componentWillmount()`方法。与老树相关的任何状态都丢弃。
例如
```
<div>
    <Counter />
</div>

<span>
   <Counter />
</span>
```
这会都会摧毁老的`Counter` 挂载新的`Counter` 

## 相同类型的DOM 元素
当比较类型相同的React DOM元素的时候。React会去比较两者的属性，只更新不同的属性。例如
```
<div className="before" title="stuff"/>
<div className="after" title="stuff">
```
通过比较上面的两个元素，React只会去修改DOM节点的`className`

当更新`style` React也只会更新改变的属性
```
<div style={{color:'red',fontWeight:'bold'}}/>
<div style={{color:'green',fontWeight:'bold'}}/>
```
上述情况，react只会更新color属性

## 相同类型的组件元素
当一个组件更新的时候，组件实例保持不变，在整个render期间，状态都会维护。React 更新组件中的props来匹配新元素，在这过程中 组件实例会调用 `componentWillReceiveProps()`和`componentWillUpdate()`,然后`render()`方法被调用，diff算法对上一个结果和旧的结果进行递归。
## 孩子节点的递归
一般来讲，当在一个DOM节点的孩子节点进行递归时.React只需要迭代孩子节点列表，并对不同点做出回应。
例如在孩子节点的末端加上一个元素。
```
<ul>
   <li>first</li>
   <li>second</li>
</ul>

<ul>
 <li>first</li>
 <li>second</li>
 <li>third</li>
</ul>
```
React将会比在依次比对，然后把`<li></li>`插入树中。

如果你仅仅是简单实现该算法，下面树的转换会，性能会变的很糟糕
```
<ul>
 <li>Duke</li>
 <li>Vilanova</li>
</ul>

<ul>
  <li>Connection</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```
React将会改变每一个孩子节点，为了达到该效果，他不知道可以不修改`<li>Duke</li>`和`<li></li>`这子树。这样效率会变得低下。

## Keys
为了解决上述的问题，React提供了一个`key`属性、 React通过`key`来比较子元素是否有更改。
```
<ul>
 <li key="2015">Duke</li>
 <li key="2016">Vilanova</li>
 <li></li>
</ul>

<ul>
 <li key="2014">Connecticut</li>
 <li key="2015">Duke</li>
 <li key="2016">Vilanova</li>
</ul>
```
这样React知道 key为`2014`是一个新的子节点，这样key为`2015`,`2016`的元素只需要移动。

key只需要在兄弟节点保存唯一，而不是在整个应用中。
## 折中
1. Key必须是稳定的，可预测的并唯一。 使用Math.random()来创建key会导致许多组件实例和DOM节点重新创建，这会导致性能下降。并丢失子元素的状态。
2. 该算法不会去匹配不同组件的子元素。如果两个组件的输出大致一致，那写成一个组件好



