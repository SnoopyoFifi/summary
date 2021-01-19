# JSAdvance

## js执行机制

## `setTimeout/setInterval`

## `Object.defineProperty/Proxy(ESNext)`

## 异步编程

  ```js
  /**
   * 包装一个promise, 简化promise的调用.
   * @param {Function} fn 
   */
  const wrapPromise = fn => {
    const promise = new Promise((resolve, reject) => {
      fn(resolve, reject);
    });

    return promise;
  };

  /**
   * sleep函数.
   * @param {Number} time 休眠时长.
   */
  const sleep = (time = 500) => wrapPromise(resolve => setTimeout(resolve, time));

  ```

## `Array.prototype.reduce`

## `Object.prototype.toString`

## `requestAnimationFrame`

## `requestIdleCallback`

## dragApi

## FileApi

## BlobAPi

## webComponents

## 装饰器

- [JavaScript 装饰器](https://www.keisei.top/understanding-javascript-decorators/)
- [装饰器详解](https://github.com/Pines-Cheng/blog/issues/49)
- [Using Decorators in JavaScript](https://dev.to/tpiros/using-decorators-in-javascript-49mb)
