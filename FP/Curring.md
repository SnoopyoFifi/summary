# 柯里化

> A curried function is a function that takes multiple arguments one at a time.
> 一个函数如果一次只接受多个参数中的一个，那么就说这个函数是柯里化函数。

## 柯里化(`Curring`)

- what

  - 柯里化是一种函数的转换，它是指将一个函数从可调用的 `f(a, b, c)` 转换为可调用的 `f(a)(b)(c)`。
  - 柯里化不会调用函数。它只是对函数进行转换。

- how

  - 使用递归。
  - 现在调用：如果提供的参数(args)足够多，则调用传递的函数fn。
  - 获取一个偏函数：否则，返回一个经过curry处理的函数fn，将之前传入的参数与新的参数一起传入。
  - 然后，在一个新的调用中，再次，我们将获得一个新的偏函数（如果参数不足的话），或者最终的结果。 ·
- code

```js
  function curry(fn) {
    var args = [];
    return function () {
      if (arguments.length === 0) {
        return fn.apply(this, args);
      } else {
        [].push.apply(args, arguments);
        return arguments.callee;
      }
    }
  };

  function curry(fn) {
    return function curried(...args) {
      if (args.length >= fn.length) {
        return fn.apply(this, args);
      } else {
        return function(..._args) {
          return curried.apply(this, args.concat(_args));
        }
      }
    }
  }

  function curry(fn) {
    const arity = fn.length;

    return function $curry(...args) {
      if (args.length < arity) {
        return $curry.bind(null, ...args);
      }

      return fn.call(null, ...args);
    }
  }

  // ESNext
  const curry = (fn, arity = fn.length, ...args) =>
    arity <= args.length ? fn(...args) : curry.bind(null, fn, arity, ...args);

  // EXAMPLES
  curry(Math.pow)(2)(10); // 1024
  // 如果想curry一个接受可变数量参数的函数(可变参数函数，例如Math.min())，可以选择将参数的数量传递给第二个形参。
  curry(Math.min, 3)(10)(50)(2); // 2
```

## 偏函数(`Partial Application`)

```js
  function partial(func, ...argsBound) {
    return function(...args) {
      return func.call(this, ...argsBound, ...args);
    }
  }

  //ESNext
  const partial = (fn, ...partials) => (...args) => fn(...partials, ...args);
  const partialRight = (fn, ...partials) => (...args) => fn(...args, ...partials);

  // EXAMPLES
  const greet = (greeting, name) => greeting + ' ' + name + '!';
  const greetHello = partial(greet, 'Hello');
  greetHello('John'); // 'Hello John!'

  // 🦏  7 - 290
  function partial(f, ...outerArgs) {
    return function(...innerArgs) {
      let args = [...outerArgs];
      let innerIndex = 0;
      for(let i = 0; i < args.length) {
        if (args[i] === undefined) args[i] = innerArgs[innerIndex++];
      }
      args.push(...innerArgs.slice(innerIndex));
      return f.apply(this, args);
    }
  }
```

## 反柯里化(`Uncurring`)

```js
  const uncurry = (fn, n = 1) => (...args) => {
    const next = acc => args => args.reduce((x, y) => x(y), acc);
    if (n > args.length) throw new RangeError('Arguments too few!');
    return next(fn)(args.slice(0, n));
  };

  // EXAMPLES
  const add = x => y => z => x + y + z;
  const uncurriedAdd = uncurry(add, 3);
  uncurriedAdd(1, 2, 3); // 6
```

## **参考文章和书籍**

- [Currying](https://javascript.info/currying-partials)
- [UnCurrying](https://www.30secondsofcode.org/js/s/uncurry)
- [partial](https://www.30secondsofcode.org/js/s/partial)
- [curry](https://www.30secondsofcode.org/js/s/curry)
