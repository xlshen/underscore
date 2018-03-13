### underscore中_是弄啥的
`_`在`underscore`中是以函数形式定义的对象，看看它的定义:
```js
  var _ = function(obj) {
      // 如果参数是"_"的实例，直接返回，相当于容错机制
      if (obj instanceof _) return obj;
      // 否则返回实例new _(obj), 继续调用该函数，下一次进来之后直接保存下一步的变量_wrapped
      if (!(this instanceof _)) return new _(obj);
      // 实例对象_wrapped属性中存储了接受的参数
      this._wrapped = obj;
  };
```
① `_`是一个函数，支持OOP调用的构造函数  
② `underscore`中的属性和方法默认都挂载在`_`下面：比如 `_.each`、`_.map`...等
#### 实例：
```js
  // 直接调用
  _.each([1, 2, 3], function(value, index){
    console.log(value); // 1, 2, 3
  });
  // OOP方式调用
  _([1, 2, 3]).each(function(value, index){
    console.log(value); // 1, 2, 3
  });
```
#### 解析：
第一种直接调用就直接调用`_.each()`方法即可，因为方法默认都是挂载在`_`上面。
