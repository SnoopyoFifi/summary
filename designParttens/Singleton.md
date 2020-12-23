# 单例模式(`Singleton Pattern`)

## 概述

> 口诀：**缓存实例、实例唯一**

- 定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点，称为单例模式。
- 应用场景：当在程序中多次使用同一个对象且作用相同时，单例模式便可以让程序仅在内存中创建一个对象，让所有需要调用的地方都共享这一单例对象。
- 实现思路：用一个变量来标志当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例时，直接返回之前创建的对象。即使用数据缓存来存储该单例，用作判断单例是否已经生成。

## 具体实现

- 不透明的单例模式

```js
  /**方法调用**/
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

  /** 闭包实现 **/
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

  /** class 实现 **/
  class Singleton {
    constructor(name) {
      this.name = name;
    }

    getName() {
      console.log(this.name);
    }

    getInstance = (function() {
      let instance = null;
      return function(name) {
        return instance || (instance = new Singleton(name));
      }
    })()
  }
```

- 透明的单例模式

> 实现一个“透明”的单例类，创建对象可以像使用其他任何普通类一样。

```js
  function CreateSingleton(name) {
    this.name = name;
    this.getName();
  }

  CreateSingleton.prototype.getName = function() {
    console.log(this.name);
  }

  let Singleton = (function(name) {
    let instance = null;
    return function(name) {
      return instance || (instance = new CreateSingleton(name));
    }
  })();

  let snoopy = new Singleton('snoopy');
  let fifi = new Singleton('fifi');
```

- 代理实现单例模式

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
    let instance;
    return function() {
      return instance || (instance = fn.apply(this, arguments));
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

## 具体应用

- 函数缓存

> 将上次计算结果缓存起来，当下次调用时，如果遇到相同参数，就直接返回缓存中的数据。
> 实现思路：把参数和对应的结果数据存在一个对象中，调用时判断参数对应的数据是否存在，存在就返回对应的结果数据，否则返回计算结果。
> 适用场景：需要大量重复计算/且计算依赖之前的结果的场景。

```js
  // 方法一
  const memoize = function(fn, content) {
    let cache = Object.create(null);
    content = content || this;
    return (...key) => {
      return cache[key] || (cache[key] = fn.apply(content, key));
    }
  }

  // 方法二
  const memoize = function(fn) {
    const cache = Object.create(null);
    return function cacheFn(...args) {
      const key = Json.stringify(args);
      return cache[key] || (cache[key] = fn.apply(fn, args))
    }
  }
```

## **参考文章和书籍**

- [JavaScript 单例模式](https://segmentfault.com/a/1190000012842251)
- [Javascript缓存函数&柯里化&偏函数](https://zhuanlan.zhihu.com/p/112505577)
- [深入理解 JavaScript 模块模式](https://blog.crimx.com/2014/08/05/javascript-module-pattern-in-depth/)
- 《Javascript 设计模式与开发实践》
