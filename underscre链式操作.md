### underscore链式操作
涉及函数：`_.chain(obj) === _(obj).chain()`
#### 实例：
```js
  // 非 OOP 链式调用
  _.chain([1, 2, 3])
  .map(function(a) {return a;})
  .reverse()
  .value(); // [3, 2, 1]

  // OOP 链式调用
  _([1, 2, 3])
  .chain()
  .map(function(a){return a;})
  .first()
  .value(); // 1
```
以上两种方式都可以达到链式调用的目的。在underscore内部实现中，这两种方式实现都是用了OOP的实现方法，具体来看实现函数：
```js
  _.chain = function(obj) {
    var instance = _(obj);
    instance._chain = true;
    return instance;
  };
```
