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
          // 挂载到_上面
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
