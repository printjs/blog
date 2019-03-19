[首页](https://printjs.github.io/blog) / [vim](https://printjs.github.io/blog/docs/react) / 介绍jsx


# 介绍JSX

考虑这个变量声明
```jsx
const element = <h1>Hello, world!</h1>;
```
有趣的是，它既不是字符串也不是html

它被称之为jsx，它是JavaScript的语法扩展。我们推荐在React中使用他来描述UI应该是什么样子的。JSX可能会让你理解为一个模版语言，但其实他全部来自于JavaScript。

## 为什么是JSX？

React认为渲染逻辑本质上与其他UI逻辑耦合这一事实：事件如何处理，状态如何随时间变化，以及数据如何准备显示。

React不是通过将标记和逻辑放在单独的文件中来人为地分离技术，而是将关注点与包含两者的称为“组件”的松散耦合单元分开。 我们将在另一部分回到组件，但如果你还不熟悉在JS中加入标记，那么这个话题可能会说服你。

React不需要使用JSX，但是大多数人发现在JavaScript代码中使用UI时它是一种有用的视觉辅助工具。 它还允许React显示更多有用的错误和警告消息。

有了这个，让我们开始吧！

## React阻止注入攻击

默认情况下， React DOM在渲染之前会转义JSX中嵌入的任何值。因此，为了确保你不被注入任何不明确的东西在你的应用中。所有的东西在渲染前都会被转义为字符串，它可以帮助我们阻止[XSS跨站脚本攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。

## JSX代表对象

Babel将jsx编译为React.createElement()调用。

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```
```jsx
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

React.createElement()执行一些检查，帮助我们执行一些无错误的代码，实际上，它会创建一个下面的对象
```js
// 注意，结构非常简单
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```
这个对象称之为"react 元素" 你可以考虑到他们作为你屏幕上看到的描述。React读这些元素并且构造他们保持到他们更新。