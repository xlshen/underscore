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
#### 解析：
以上两种方式都可以达到链式调用的目的。在underscore内部实现中，这两种方式实现都是用了OOP的实现方法，具体来看实现函数：
```js
  _.chain = function(obj) {
    var instance = _(obj);
    instance._chain = true;
    return instance;
  };
```
`_.chain(obj)`通过调用`chain()`方法将`_(obj)`先转化为`_`的实例对象，然后将实例对象添加`_chain`属性，返回该实例对象。  
而对于`_(obj).chain()`来说，首先通过`_(obj)`创建`_`的实例对象，然后该实例调用`_`对象原型中的`chain()`方法【该方法通过`_.mixin()`方法混入到`_.prototype`中】详见：[underscore中OOP思想—实例对象方法调用实现机制](http://)，

该方法第一步因为`_(obj) instanceof _ === true`会直接返回该实例，然后给该实例添加`_chain`属性。在此该两种方法都是初始化实例，然后添加`_chain`属性  
【注：对于`_(obj)`不熟悉的可以移步
[underscore中的_是弄啥的？？？](https://github.com/xlshen/underscore/blob/master/underscore中的_是弄啥的.md)】
