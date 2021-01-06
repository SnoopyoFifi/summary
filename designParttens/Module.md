# 模块模式(`Module Pattern`)

## 概述

> 口诀：**私有变量、公共接口**

- 定义：模块是指一个拥有公共接口的封闭命名空间。
- 应用场景：创建对象时，需要进行内部初始化，同时对内部属性和方法有访问权限限制，就需要使用模块模式。
- 实现思路：利用 闭包（作用域链）原理实现私有变量，保护模块内部变量无法从模块外部修改。

## 具体实现

```js
  // 常规使用
  const myModule = (function() {
    let privateVar = 0;
    let privateFn = function(arg) {
      console.log(arg);
    };

    return {
      publicVar: 'foo',
      publicFn: function(arg) {
        privateVar++;
        privateFn(arg);
      }
    }
  }());

  /**增强的模块模式**/
  function CustomType() {
    this.name = 'snoopy';j
  }
  CustomType.prototype.getName = function() {
    return this.name;
  }

  const myModule = (function() {
    // 私有属性和方法
    const privateVar = 0;
    function privateFn() {};

    // 获取需要增强的对象
    const exports = new CustomType();
    
    // 为对象添加公共属性和方法
    exports.publicVar = 'publicA';
    exports.publicFn = function() {
      privateVar++;
      return privateVar;
    };
    return exports;
  })();

```

## 具体应用

> 模块模式在 jQuery 等一些 JS 库中都有广泛的应用。

## **参考文章和书籍**

- [JS 设计模式](https://set.sh/post/180612-js-design-pattern)
- [深入理解 JavaScript 模块模式](https://blog.crimx.com/2014/08/05/javascript-module-pattern-in-depth/)
