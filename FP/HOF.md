# 高阶函数(`Higher Order Functions`)

> A higher order function is a function that takes a function as an argument, or returns a function.
> 接收函数为参数的或者直接返回函数的函数就是高阶函数。

## reduce

## map

## filter

## memoization

```js
  function memoize(f) {
    const cache = new Map();

    return function(...args) {
      let key = args.length + args.join('+');
      if (cache.has(key)) {
        return cache.get(key);
      } else {
        let result = f.apply(this, args);
        cache.set(key, result);
        return result;
      }
    }
  }

  const memoize = fn => new Proxy(fn, {
    cache: new Map(),
    apply (target, thisArg, argsList) {
      let cacheKey = argsList.toString();
      if(!this.cache.has(cacheKey)) {
        this.cache.set(cacheKey, target.apply(thisArg, argsList));
      }
      return this.cache.get(cacheKey);
    }
  });

  const fibonacci = n => (n <= 1 ? 1 : fibonacci(n - 1) + fibonacci(n - 2));
  const memoizedFibonacci = memoize(fibonacci);

  for (let i = 0; i < 100; i ++)
    fibonacci(30);                      // ~5000ms
  for (let i = 0; i < 100; i ++)
    memoizedFibonacci(30);              // ~50ms
```

## 延伸阅读

- [Where and how can I use memoization in JavaScript?](https://www.30secondsofcode.org/blog/s/javascript-memoization)