[首页](https://printjs.github.io/blog) / [vim](https://printjs.github.io/blog/docs/react) / 渲染元素


# 渲染元素

## 更新React元素
React的元素是不可变的。因此，你一旦创建了元素。你不能改变他的孩子或者他的属性。一个元素就像单独的一个框架在电影中。它只会呈现在指定的时间上。

根据现在知识，只有一种方法能更新元素，那就是创建一个新的元素，并且使用`ReactDOM.render()`

考虑这个时钟的例子
```js
function tick() {
    const element = (
        <div>
            <h1>Hello, world!</h1>
            <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
    );
    ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);
```
每个1秒钟，都会更新这个元素

## React仅仅更新需要的元素
React比较孩子节点和之前的元素。仅仅更新期望的状态

<img src="./render1.gif"/>
