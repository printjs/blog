[首页](https://printjs.github.io/blog) / [knowledge](https://printjs.github.io/blog/docs/knowledge) / defineProperties

# defineProperties
Object.defineProperties本质上定义了obj 对象上props的可枚举属性相对应的所有属性。

## api
`Object.defineProperties(obj, props)`

```js
var obj = {};
Object.defineProperties(obj, {
  'property1': {
    value: true,
    writable: true
  },
  'property2': {
    value: 'Hello',
    writable: false
  }
  // etc. etc.
});
```
具体参数含义，请参与[Object.defineProperty](https://printjs.github.io/blog/docs/knowledge/defineproperty)