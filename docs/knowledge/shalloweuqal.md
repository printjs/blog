[首页](https://printjs.github.io/blog) / [knowledge](https://printjs.github.io/blog/docs/knowledge) / 浅等于

# 浅等于

转自[https://imweb.io/topic/598973c2c72aa8db35d2e291](https://imweb.io/topic/598973c2c72aa8db35d2e291)

## 背景
> 在学习react PureComponent的时候，看到有一句话，由于PureComponent的shouldeComponentUpdate里，实际是对props/state进行了一个浅对比，所以对于嵌套的对象不适用，没办法比较出来。那什么是浅对比呢，为什么对于嵌套的对象就不适用了呢？

`shallowEqual`
在React里，shouldComponentUpdate源码为：
```js
if (this._compositeType === CompositeTypes.PureClass) {
  shouldUpdate = !shallowEqual(prevProps, nextProps) || ! shallowEqual(inst.state, nextState);
}
```
可以看到PureComponent就是对props跟state的前后状态做了一个浅比较。

我们在顺藤摸瓜，看看shallowEqual的源码长啥样
```js
const hasOwn = Object.prototype.hasOwnProperty

function is(x, y) {
  if (x === y) {
    return x !== 0 || y !== 0 || 1 / x === 1 / y
  } else {
    return x !== x && y !== y
  }
}

export default function shallowEqual(objA, objB) {
  if (is(objA, objB)) return true

  if (typeof objA !== 'object' || objA === null ||
      typeof objB !== 'object' || objB === null) {
    return false
  }

  const keysA = Object.keys(objA)
  const keysB = Object.keys(objB)

  if (keysA.length !== keysB.length) return false

  for (let i = 0; i < keysA.length; i++) {
    if (!hasOwn.call(objB, keysA[i]) ||
        !is(objA[keysA[i]], objB[keysA[i]])) {
      return false
    }
  }

  return true
}
```
这段代码在讲什么鬼？一脸懵逼啊！
```
Object.is()
```
在解析shallowEqual的源码之前，先来认识一下`Object.is()`，这个函数是用来比较两个值是否相等。

为什么要用这个来比较而不是 `==` 或者 `===` 呢？

## `==`
首先先看 ==，由于JS是弱类型的，如果使用 == 进行比较，== 操作符会自动将 0，‘ ’（空字符串），null，undefined 转成布尔型false，这样就会出现

0 == ' '  // true
null == undefined // true
[1] == true // true
这显然是不符合预期的。所以JS为我们提供了全等操作符 ===，它不会进行类型转换，也就是说如果两个值一样，必须符合类型也一样。但是，它还是有两种疏漏的情况

+0 === -0 // true，但我们期待它返回false
NaN === NaN // false，我们期待它返回true
所以，Object.is修复了=== 这两种判断不符合预期的情况，
```js
function(x, y) {
    // SameValue algorithm
    if (x === y) {
     // 处理为+0 != -0的情况
      return x !== 0 || 1 / x === 1 / y;
    } else {
    // 处理 NaN === NaN的情况
      return x !== x && y !== y;
    }
};
```
这样就使得Object.is()总是返回我们需要的结果。 它在下面6种情况下，会返回true

两个值都是 undefined
两个值都是 null
两个值都是 true 或者都是 false
两个值是由相同个数的字符按照相同的顺序组成的字符串
两个值指向同一个对象
两个值都是数字并且
都是正零 +0
都是负零 -0
都是 NaN
都是除零和 NaN 外的其它同一个数字
可以看出Object.is可以对基本数据类型:null,undefined,number,string,boolean做出非常精确的比较，但是对于引用数据类型是没办法直接比较的。

## 剖析shallowEquall
```js
// 用原型链的方法
const hasOwn = Object.prototype.hasOwnProperty

// 这个函数实际上是Object.is()的polyfill
function is(x, y) {
  if (x === y) {
    return x !== 0 || y !== 0 || 1 / x === 1 / y
  } else {
    return x !== x && y !== y
  }
}

export default function shallowEqual(objA, objB) {
  // 首先对基本数据类型的比较
  if (is(objA, objB)) return true
  // 由于Obejct.is()可以对基本数据类型做一个精确的比较， 所以如果不等
  // 只有一种情况是误判的，那就是object,所以在判断两个对象都不是object
  // 之后，就可以返回false了
  if (typeof objA !== 'object' || objA === null ||
      typeof objB !== 'object' || objB === null) {
    return false
  }

  // 过滤掉基本数据类型之后，就是对对象的比较了
  // 首先拿出key值，对key的长度进行对比

  const keysA = Object.keys(objA)
  const keysB = Object.keys(objB)

  // 长度不等直接返回false
  if (keysA.length !== keysB.length) return false
  // key相等的情况下，在去循环比较
  for (let i = 0; i < keysA.length; i++) {
  // key值相等的时候
  // 借用原型链上真正的 hasOwnProperty 方法，判断ObjB里面是否有A的key的key值
  // 属性的顺序不影响结果也就是{name:'daisy', age:'24'} 跟{age:'24'，name:'daisy' }是一样的
  // 最后，对对象的value进行一个基本数据类型的比较，返回结果
    if (!hasOwn.call(objB, keysA[i]) ||
        !is(objA[keysA[i]], objB[keysA[i]])) {
      return false
    }
  }

  return true
}
```
## 总结
回到最开始的问题，浅比较为什么没办法对嵌套的对象比较？

由上面的分析可以看到，当对比的类型为Object的时候并且key的长度相等的时候，浅比较也仅仅是用Object.is()对Object的value做了一个基本数据类型的比较，所以如果key里面是对象的话，有可能出现比较不符合预期的情况，所以浅比较是不适用于嵌套类型的比较的。

参考资料：
[http://www.jstips.co/zh_cn/javascript/why-you-should-use-Object.is()-in-equality-comparison/](http://www.jstips.co/zh_cn/javascript/why-you-should-use-Object.is()-in-equality-comparison/)

[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)
