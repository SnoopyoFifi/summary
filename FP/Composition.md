# 组合函数

- [](https://segmentfault.com/u/dongzhe3917875/articles)
- [【函数工具】compose组合式函数](https://www.jianshu.com/p/f42d941dcd23)
- [koa-compose](https://www.cnblogs.com/caoke/p/9969945.html)

> 什么是组合函数，组合函数的使用场景，为什么要用组合函数的思想？

## `curry`

## `partials`

```js
  // function partial() {
  //   let fn = this,
  //   boundArgs = Array.prototype.slice.call(arguments);
  //   let placeholder = <<partialPlaceholderObj>>;

  //   let bound = function() {
  //     let position = 0, length = args.length;
  //     for(let i = 0; i < length; i++) {
  //       args[i] = boundArgs[i] === placeholder ? arguments[position++] : boundArgs[i];
  //     }
      
  //     while(position < argumen)
  //   }
  // }
```

## `compose`

```js
  const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);
  const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x);


  // compose :: ((y -> z), (x -> y), ..., (a -> b)) -> a -> z
  const compose = (...fns) => (...args) => fns.reduceRight((res, fn) => [fn.call(null, ...res)], args)[0];

  // 30secondsofcode
  const compose = (...fns) => fns.reduce((f, g) => (..args) => f(g(...args)));

  // Examples
  const add5 = x => x + 5;
  const multiply = (x, y) => x * y;
  const multiplyAndAdd5 = compose(
    add5,
    multiply
  );
  multiplyAndAdd5(5, 2); // 15

  // 30secondsofcode
  const pipe = (...fns) => fns.reduce((f, g) => (..。args) => g(f(...args)));
  const pipeAsyncFunctions = (...fns) => arg => fns.reduce((p, f) => p.then(f), Promise.resolve(arg));

  const sum = pipeAsyncFunctions(
    x => x + 1,
    x => new Promise(resolve => setTimeout(() => resolve(x + 2), 1000)),
    x => x + 3,
    async x => (await x) + 4
  );
  (async() => {
    console.log(await sum(5)); // 15 (after one second)
  })();

  // [JavaScript 函数式编程指南] - 93
  function compose(/* fns */) {
    let args = arguments;
    let start = arguments.length - 1;
    return function() {
      let i = start;
      let result = args[start].apply(this, arguments);
      while(i--) {
        result = args[i].call(this, result);
      }
      return result;
    }
  }

  // redux
  export default function compose(...funcs) {
    if (funcs.length === 0) {
      return args;
    }
    if (funcs.length === 1) {
      return funcs[0];
    }
    return funcs.reduce((a, b) => (...args) => a(b(...args)))
  }

  // lodash - flow/flowRight
  function flow(...funcs) {
    const length = funcs.length;
    let index = length;
    while(index--) {
      if (typeof funcs[index] !== 'function') {
        throw new TypeError('Expected a function');
      }
    }
    return function(...args) {
      let index = 0;
      let result = length ? funcs[index].apply(this, args) : args[0];
      while(++index < length) {
        result = funcs[index].call(this, result);
      }
      return result;
    }
  }

  function flowRight(...funcs) {
    return flow(...funcs.reverse());
  }

  // koa - https://github.com/koajs/compose/blob/master/index.js
  
```

## 延伸阅读

- [mostly-adequate-guide](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/appendix_a.md)