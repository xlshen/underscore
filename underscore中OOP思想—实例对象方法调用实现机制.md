### underscore中OOP思想—实例对象方法调用实现机制
默认情况下，`underscore`的方法都是挂载到`_`对象下面。那在OOP中怎么实现实例对象方法的调用，也就说如何把`_`下面方法挂载到`_`的原型对象中。那就是`_.mixin()`方法，源码实现：
```js
  // Add your own custom functions to the Underscore object.
  // 混入函数
  // ① 挂载_下面的函数到_.prototype原型上
  // ② 扩展underscroe库函数，参数为对象，方法在对象的属性上
  _.mixin = function(obj) {
      // 遍历对象下面所有方法挂载到_上面和_.prototype上面
      _.each(_.functions(obj), function(name) {
          // 挂载到_上面【此处为扩展，用户可以自定义自己的对象方法混入到_对象上面】
          var func = _[name] = obj[name];
          // 挂载到_.prototype上面
          _.prototype[name] = function() {
              // 第一个参数为OOP的对象，例如：_([1, 2, 3]).each(function(){})中，this._warpped = [1, 2, 3];
              var args = [this._wrapped];
              // arguments为name需要的其他参数,例如：_([1, 2, 3]).each(function(){}, this)中,arguments为function(){}和this
              push.apply(args, arguments);
              // 执行func函数，支持链式操作
              return chainResult(this, func.apply(_, args));
          };
      });
      return _;
  };
  // Add all of the Underscore functions to the wrapper object.
  // 混入_对象
  _.mixin(_);
```
实例：
```js
  // 自定义对象方法，扩展到_对象
  var _obj = {
    tips: function(obj, msg){
      console.log(obj, msg);
    }
  }
  _.mixin(_obj);
  _.tips([1,2,3], "underscore"); // [1, 2, 3] "underscore"
  _([1,2,3]).tips("underscore"); // [1, 2, 3] "underscore"
```
解析：
`_.mixin()`方法将目标对象中的方法扩展到`_`和`_.prototype`对象上，之后可以直接方法调用和OOP实例调用使用。在通过OOP实例调用时，内部函数获取到传入的`this._wrapped`对象，然后和调用参数一起赋值给`args`变量，最后在`chainResult`函数中调用执行。
【注：对于`chainResult`函数不清楚的移步：[underscore链式操作](https://github.com/xlshen/underscore/blob/master/underscre%E9%93%BE%E5%BC%8F%E6%93%8D%E4%BD%9C.md)】
