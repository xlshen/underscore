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
【注：对于`_(obj)`不熟悉的可以移步
[underscore中的_是弄啥的？？？](https://github.com/xlshen/underscore/blob/master/underscore%E4%B8%AD%E7%9A%84_%E6%98%AF%E5%BC%84%E5%95%A5%E7%9A%84.md)】

而对于`_(obj).chain()`来说，首先通过`_(obj)`创建`_`的实例对象，然后该实例调用`_`对象原型中的`chain()`方法【该方法通过`_.mixin()`方法混入到`_.prototype`中】详见：[underscore中OOP思想—实例对象方法调用实现机制](https://github.com/xlshen/underscore/blob/master/underscore%E4%B8%ADOOP%E6%80%9D%E6%83%B3%E2%80%94%E5%AE%9E%E4%BE%8B%E5%AF%B9%E8%B1%A1%E6%96%B9%E6%B3%95%E8%B0%83%E7%94%A8%E5%AE%9E%E7%8E%B0%E6%9C%BA%E5%88%B6.md)。  
`_.prototype['chain']`方法最后一步：
```js
  // _.mixin()方法中_.prototype[name] = function() {...}最后返回
  return chainResult(this, func.apply(_, args));
  // chainResult实现函数
  var chainResult = function(instance, obj) {
      return instance._chain ? _(obj).chain() : obj;
  };
```
`chainResult(this, func.apply(_, args))`方法中`this`是`_`对象，`func.apply(_, args)`调用`_.chain()`方法返回传入对象的实例，在上面例子中就是`[1, 2, 3]`对象的实例对象，并给该对象添加`_chain`属性，返回该实例对象。那此时调用为`chain(_对象, [1, 2, 3]添加了_chain属性的实例对象)`，`_._chain === undefined`所以直接返回添加了`_chain`属性的`[1, 2, 3]`对象实例。

到这里是不是发现了什么？！Yes，`_.chain(obj)`和`_(obj).chain()`回到了同一起跑线！

再梳理一下其中逻辑：
