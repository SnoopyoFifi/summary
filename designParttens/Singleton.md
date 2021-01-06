# 单例模式(`Singleton Pattern`)

## 概述

> 口诀：**缓存实例、实例唯一**

- 定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点，称为单例模式。
- 应用场景：作用域中只应存在唯一一个具有公共接口的类的实例，如创建弹窗。
- 实现思路：单例模式通过模块内部的一个变量 `instance` 来作为实例的缓存容器，通过判断是否存在实例缓存来决定是否执行实例化。据此实现全局只存在唯一一个 `singleton` 的实例的逻辑。

## 具体实现

- 不透明的单例模式

  ```js
  // 方法调用
  function Singleton(name) {
    this.name = name;
  }
  
  Singleton.instance = null;
  
  Singleton.getInstance = function(name) {
    return this.instance || (this.instance = new Singleton(name));
  }
  
  let snoopy = Singleton.getInstance('snoopy');
  let fifi = Singleton.getInstance('fifi');
  
  console.log(snoopy === fifi);
  console.dir(Singleton);
  
  // 闭包实现
  function Singleton(name) {
    this.name = name;
  }
  
  Singleton.prototype.getName = function() {
    console.log(this.name);
  }
  
  Singleton.getInstance = (function() {
    let instance = null;
    return function(name) {
      return instance || (instance = new Singleton(name));
    }
  })()

  // 闭包变种
  const singleton = (function() {
    function init() {
      let privateNum = 10;
      const privateFn = function() {};

      return {
        num: privateNum,
        fn: privateFn,
      }
    }

    let _instance = null;

    return {
      getInit() {
        _instance = _instance || init();
        return _instance;
      }
    }
  })()

  const ins1 = singleton.getInit();
  const ins2 = singleton.getInit();
  console.log(ins1 === ins2);
  
  // class 实现
  class Singleton {
    constructor(name) {
      this.name = name;
    }
  
    getName() {
      console.log(this.name);
    }
  
    static getInstance = (function() {
      let instance = null;
      return function(name) {
        return instance || (instance = new Singleton(name));
      }
    })()
  }
  ```

- 透明的单例模式

  > 实现一个“透明”的单例类，创建对象可以使用 `new`。

  ```js
  var Singleton = (function() {
    var instance = null;
  
    var CreateDiv = function(html) {
      if (instance) {
        return instance;
      }
      this.html = html;
      this.init();
  
      return instance = this;
  };
  
    CreateDiv.prototype.init = function() {
      var div = document.createElement('div');
      div.innerHTML = this.html;
      document.body.appendChild(div);
    }

    return CreateDiv;
  })();

  let snoopy = new Singleton('snoopy');
  let fifi = new Singleton('fifi');
  ```
  
- 代理实现单例模式
  
  > 抽离负责管理单例的逻辑到代理类中，创建对象和初始化`init`方法和代理类组合实现创建单例，遵循了**单一职责的原则**

  ```js
  const CreateDiv = function(html) {
    this.html = html;
    this.init();
  }
  
  CreateDiv.prototype.init = function() {
    let div = document.createElement('div');
    div.innerHTML = this.html;
    document.body.appendChild(div);
  }
  
  // 代理构造函数
  const ProxySingletonCreateDiv = (function(){
    var instance;
    return function(html){
      return instance || (instance = new CreateDiv(html));
    }
  })();
  
  let snoopy = new ProxySingletonCreateDiv('snoopy');
  let fifi = new ProxySingletonCreateDiv('fifi');
  ```

- **惰性单例模式(通用单例模式)**

  > 在需要的时候才创建对象实例

  ```js
  let singleton = function(fn) {
    let cache = null;
    return function(...args) {
      return cache || (cache = fn.apply(this, args));
    }
  }
  
  let createNode = function(nodeName){
    let node = document.createElement(nodeName);
    document.body.appendChild(node);
    return node;
  }
  
  let createSingleIframe = singleton(createNode);
  
  let loginLayer = createSingleIframe('iframe');
  loginLayer.src = 'http://baidu.com';
  ```

- 使用`Proxy API`实现
  
  > 使用 `Proxy` 提供的 `construct` 可以扩展目标类的构造函数，为 `new` 提供一个 **trap** 以实现创建单例的目的。

  ```js
  const singletonify = (className) => {
    return new Proxy(className.prototype.constructor, {
      instance: null,
      construct: (target, argumentsList) => {
        return this.instance || (this.instance = new target(...argumentsList));
      }
    })
  }
  
  // 实例
  class MyClass {
    constructor(msg) {
      this.msg = msg;
    }
  
    printMsg() {
      console.log(this.msg);
    }
  }
  const MySingletonClass = singletonify(MyClass);
  const myObj = new MySingletonClass('fisrt');
  myObj.printMsg();
  const myObj2 = new MySingletonClass('second');
  myObj.printMsg();
  ```

## 具体应用

- 函数缓存

> 将上次计算结果缓存起来，当下次调用时，如果遇到相同参数，就直接返回缓存中的数据。
> 实现思路：把参数和对应的结果数据存在一个对象中，调用时判断参数对应的数据是否存在，存在就返回对应的结果数据，否则返回计算结果。
> 适用场景：需要大量重复计算/且计算依赖之前的结果的场景。

  ```js
  // es5 《Javascript 设计模式与开发实践》101
  var createProxyFactory = function( fn ){
    var cache = {};
    return function(){
      var args = Array.prototype.join.call( arguments, ',' );
      if ( args in cache ){
        return cache[args];
      }
      return cache[args] = fn.apply(this, arguments);
    }
  };

  // es6
  const memoize = function(fn) {
    const cache = Object.create(null);
    return function cacheFn(...args) {
      const key = JSON.stringify(args);
      return cache[key] || (cache[key] = fn.apply(fn, args))
    }
  }
  ```

## **参考文章和书籍**

- 《Javascript 设计模式与开发实践》
- [JavaScript 单例模式](https://segmentfault.com/a/1190000012842251)
- [Javascript缓存函数&柯里化&偏函数](https://zhuanlan.zhihu.com/p/112505577)
- [How can I implement a singleton in JavaScript?](https://www.30secondsofcode.org/blog/s/javascript-singleton-proxy)
- [Learning JavaScript Design Patterns](https://set.sh/post/180612-js-design-pattern#单例模式)
