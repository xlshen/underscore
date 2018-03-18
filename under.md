### underscore对象浅拷贝核心
核心函数：`createAssigner()`
#### 源码：
```js
  // An internal function for creating assigner functions.
  // 浅拷贝代码核心
  // 内部调用函数：_.extend(), _.extendOwn(), _.defaults()
  // 1. _.extend = createAssigner(_.allKeys);
  // 2. _.extendOwn = _.assign = createAssigner(_.keys);
  // 3. _.defaults = createAssigner(_.allKeys, true);
  // 文章号：[underscore对象浅拷贝核心](https://github.com/xlshen/underscore/issues/5)
  // keysFunc为_.key或者_.allKeys方法，返回对象的属性名组成的数组
  var createAssigner = function(keysFunc, defaults) {
      // 返回函数闭包，通过传入的对象执行以下函数
      return function(obj) {
          // 获取传入执行函数参数个数
          var length = arguments.length;
          // 如果defaults执行true，再对传入对象包装一下，此时如果obj === null || undefined
          // 则obj也转换为对象，则执行下一步时就不会因为obj为null而返回null
          if (defaults) obj = Object(obj);
          // 如果参数只有原始对象一个或者没有的情况，或者obj值为null的话，直接返回该对象
          // 1. _.extend()和_.extendOwn()调用是defaults为undefined，此时length<2或者obj值为null时，返回该对象
          // 2. _.defaults()调用时，defaults为true，此时length<2时，返回该对象【注：此时obj不可能为null，因为即使原始传入的obj为null，经过上一步操作，null也会转换为Object对象】
          if (length < 2 || obj == null) return obj;
          // _.extendOwn(destination, source1, source2, ...) 外层循环从source1开始取值
          // 外层循环参数取源对象source1，source2...，从source1开始，顺序往后执行
          for (var index = 1; index < length; index++) {
                  // 获取源对象
              var source = arguments[index],
                  // 返回源对象中的属性名数组
                  keys = keysFunc(source),
                  // 源对象属性名数组长度
                  l = keys.length;
              // 内层循环源对象属性名数组
              for (var i = 0; i < l; i++) {
                  // 获取属性名
                  var key = keys[i];
                  // 1. _.extendOwn(), _.extend()执行时defaults === false，直接覆盖目标对象相应的属性值
                  // 2. _.defaults()执行时defaults === true且目标对象当前属性为undefined时，赋值该对象该属性值；如果该对象该属性存在，不执行任何操作
                  if (!defaults || obj[key] === void 0) obj[key] = source[key];
              }
          }
          // 返回目标对象
          return obj;
      };
  };
```
> 内部调用函数：`_.extend() || _.extendOwn() || _.defaults()`

```js
  // Fill in a given object with default properties.
  // 扩展目标对象，但是禁止扩展修改目标对象已经存在的属性键值对
  _.defaults = createAssigner(_.allKeys, true);
  // Extend a given object with all the properties in passed-in object(s).
  // 扩展目标对象，对于源对象及其原型链中所有可枚举属性对目标对象进行扩展修改
  _.extend = createAssigner(_.allKeys);

  // Assigns a given object with all the own properties in the passed-in object(s).
  // (https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
  // 扩展目标对象，对于源对象仅获取自身可枚举属性扩展修改目标对象，但是源对象原型链属性不进行扩展修改
  _.extendOwn = _.assign = createAssigner(_.keys);
```
