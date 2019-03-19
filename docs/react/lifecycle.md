[首页](https://printjs.github.io/blog) / [vim](https://printjs.github.io/blog/docs/react) / 状态和生命周期

# 状态和生命周期

## 使用正确的状态
```js
// 错误
this.state.comment = 'Hello';
// 代替，使用setState
```
```js
// 正确
this.setState({comment: 'Hello'});
```

## 状态更新可以是异步

React在一次运行更新中批量执行多个`setState`

因为`this.state` 和`this.props`可以是异步的更新。所以不应该依赖它们的值来计算下一个状态。

```js
// 错误
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为了修复它，可以使用第二种方式，一种函数来代替对象

```js
// 正确
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```
我们上面使用了箭头函数，你也可以使用普通的函数
```js
// 正确
this.setState(function(state, props) {
  return {
    counter: state.counter + props.increment
  };
});
```

## 合并状态并更新

当你调用setState时，React通过给你的值来合并对象。

举个例子，你的状态可能会包含独立的值
```js
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```
然后你调用了setState
```js
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```
合并很浅，因此`this.setState（{comments}）`使`this.state.posts`保持不变，但完全取代`this.state.comments`。
