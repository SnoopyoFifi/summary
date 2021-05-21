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

## Proxy 应用

### [普通对象扩展数组方法](https://www.30secondsofcode.org/blog/s/javascript-object-array-proxy)

```js
const toKeyedArray = obj => {
  const methods = {
    map(target) {
      return callback =>
        Object.keys(target).map(key => callback(target[key], key, target));
    },
    forEach(target) {
      return callback =>
        Object.keys(target).forEach(key => callback(target[key], key, target));
    },
    reduce(target) {
      return (callback, accumulator) => 
        Object.keys(target).reduce(
          (acc, key) => callback(acc, target[key], key, target),
          accumulator
        )
    },
    filter(target) {
      return callback =>
        Object.keys(target).reduce((acc, key) => {
          if (callback(target[key], key, target)) acc[key] = target[key];
          return acc;
        }, {});
    },
    find(target) {
      return callback => {
        return (Object.entries(target).find(([key, value]) => 
          callback(value, key, target)
        ) || [])[0];
      }
    },
    findKey(target) {
      return callback =>
        Object.keys(target).find(key => callback(target[key], key, target));
    },
    includes(target) {
      return val => Object.values(target).includes(val);
    },
    keyOf(target) {
      return value =>
        Object.keys(target).find(key => target[key] === value) || null;
    },
    lastKeyOf(target) {
      return value =>
        Object.keys(target)
          .reverse()
          .find(key => target[key] === value) || null;
    },
  };

  const methodKeys = Object.keys(methods);
  
   const handler = {
    get(target, prop, receiver) {
      if (methodKeys.includes(prop)) return methods[prop](...arguments);
      const [keys, values] = [Object.keys(target), Object.values(target)];
      if (prop === 'length') return keys.length;
      if (prop === 'keys') return keys;
      if (prop === 'values') return values;
      if (prop === Symbol.iterator)
        return function* () {
          for (value of values) yield value;
          return;
        };
      else return Reflect.get(...arguments);
    },
  };

  return new Proxy(obj, handler);
}

// Examples

// Object creation
const x = toKeyedArray({ a: 'A', b: 'B' });

// Accessing properties and values
x.a;          // 'A'
x.keys;       // ['a', 'b']
x.values;     // ['A', 'B']
[...x];       // ['A', 'B']
x.length;     // 2

// Inserting values
x.c = 'c';    // x = { a: 'A', b: 'B', c: 'c' }
x.length;     // 3

// Array methods
x.forEach((v, i) => console.log(`${i}: ${v}`)); // LOGS: 'a: A', 'b: B', 'c: c'
x.map((v, i) => i + v);                         // ['aA', 'bB, 'cc]
x.filter((v, i) => v !== 'B');                  // { a: 'A', b: 'B' }
x.reduce((a, v, i) => ({ ...a, [v]: i }), {}); 	// { A: 'a', B: 'b', c: 'c' }
x.slice(0, 2);                                  // ['A', 'B']
x.slice(-1);                                    // ['c']
x.find((v, i) => v === i);                      // 'c'
x.findKey((v, i) => v === 'B');                 // 'b'
x.includes('c');                                // true
x.includes('d');                                // false
x.keyOf('B');                                   // 'b'
x.keyOf('a');                                   // null
x.lastKeyOf('c');                               // 'c'
```

## reduce 应用

### get

> 从对象中检索由给定选择器指示的一组属性

```js
  const get = (from, ...selectors) =>
    [...selectors].map(s =>
      s.
        .replace(/\[([^\[\]]*)\]/g, '.$1.')
        .split('.')
        .filter(t => t !== '')
        .reduce((prev, cur) => prev && prev[cur], from)
    );

  // Examples
  const obj = {
    selector: {
      to: {
        val: 'val to selector'
      }
    },
    target: [
      1, 2, { a: 'test' }
    ],
  };

  get(obj, 'selector.to.val', 'target[0]', 'target[2].a');
```

### deepGet

> 基于键数组获取嵌套JSON对象中的目标值。

```js
const deepGet = (obj, keys) =>
  keys.reduce(
    (xs, x) => (xs && xs[x] !== null && xs[x] !== undefined ? xs[x] : null),
    obj
  );
Examples
let index = 2;
const data = {
  foo: {
    foz: [1, 2, 3],
    bar: {
      baz: ['a', 'b', 'c']
    }
  }
};
deepGet(data, ['foo', 'foz', index]); // get 3
deepGet(data, ['foo', 'bar', 'baz', 8, 'foz']); // null
```

### pickBy

> 创建一个由给定函数返回true的属性组成的对象。

```js
const pickBy = (obj, fn) =>
  Object.keys(obj)
  .filter(k => fn(obj[k], k))
  .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});

// Example
pickBy({ a: 1, b: '2', c: 3 }, x => typeof x === 'number');
```

### OmitBy

> 省略与给定函数返回false的对象的键对应的键值对。

```js
const omitBy = (obj, fn) =>
  Object.keys(obj)
    .filter(k => !fn(obj[k], k))
    .reduce((acc, key) => ((acc[key] = obj[key]), acc), {});
// Examples
omitBy({ a: 1, b: '2', c: 3 }, x => typeof x === 'number'); // { b: '2' }
```

### dig

> 根据给定的键获取嵌套JSON对象中的目标值。

```js
const dig = (obj, target) =>
  target in obj
    ? obj[target]
    : Object.values(obj)
      .reduce((acc, key) => {
        if (acc !== undefined) return acc;
        if (typeof val === 'object') return dig(val, target);
      }, undefined);

// Examples
const data = {
  level1: {
    level2: {
      level3: 'some thing'
    }
  }
};

dig(data, 'level3');
dig(data, 'level4');
```

### renameKeys

> 用提供的值替换多个对象键的名称

```js
const renameKeys = (keysMap, obj) =>
  Object.keys(obj).reduce(
    (acc, key) => ({
      ...acc,
      ...{ [keysMap[key] || key]: obj[key] }
    }),
    {}
  );

// Examples

const obj = { name: 'Bobo', job: 'Front-End Master', shoeSize: 100 };
renameKeys({ name: 'firstName', job: 'passion' }, obj);
```

### findKeys

> 在提供的对象中查找与给定值匹配的所有键

```js
const findKeys = (obj, val) => 
  Object.keys(obj).filter(key => obj[key] === val);

// Examples

const ages = {
  Leo: 20,
  Zoey: 21,
  Jane: 20,
};
findKeys(ages, 20); // [ 'Leo', 'Jane' ]
```

### mapValues

> 使用提供的函数映射对象的值，生成具有相同键的新对象。

```js
const mapValues = (obj, fn) =>
  Object.keys(obj).reduce((acc, k) => {
    acc[k] = fn(obj[k], k, obj);
    return acc;
  }, {});
Examples
const users = {
  fred: { user: 'fred', age: 40 },
  pebbles: { user: 'pebbles', age: 1 }
};
mapValues(users, u => u.age); // { fred: 40, pebbles: 1 }
```

### mapKeys

> 使用提供的函数映射对象的键，生成一个新的对象

```js
const mapKeys = (obj, fn) =>
  Object.keys(obj).reduce((acc, k) => {
    acc[fn(obj[k], k, obj)] = obj[k];
    return acc;
  }, {});

// Examples

mapKeys({ a: 1, b: 2 }, (val, key) => key + val); // { a1: 1, b2: 2 }
```

### deepMapKeys

> 深度映射对象的键

```js
const deepMapKeys = (obj, fn) =>
  Array.isArray(obj)
    ? obj.map(val => deepMapKeys(val, fn))
    : typeof obj === 'object'
    ? Object.keys(obj).reduce((acc, current) => {
        const key = fn(current);
        const val = obj[current];
        acc[key] =
          val !== null && typeof val === 'object' ? deepMapKeys(val, fn) : val;
        return acc;
      }, {})
    : obj;

// Examples

const obj = {
  foo: '1',
  nested: {
    child: {
      withArray: [
        {
          grandChild: ['hello']
        }
      ]
    }
  }
};
const upperKeysObj = deepMapKeys(obj, key => key.toUpperCase());
/*
{
  "FOO":"1",
  "NESTED":{
    "CHILD":{
      "WITHARRAY":[
        {
          "GRANDCHILD":[ 'hello' ]
        }
      ]
    }
  }
}
*/
```

### compactObject

> 从对象或数组中深度删除所有假值

```js
const compactObject = val => {
  const data = Array.isArray(val) ? val.filter(Boolean) : val;
  return Object.keys(data).reduce(
    (acc, key) => {
      const value = data[key];
      if (Boolean(value))
        acc[key] = typeof value === 'object' ? compactObject(value) : value;
      return acc;
    },
    Array.isArray(val) ? [] : {}
  );
};

// Examples

const obj = {
  a: null,
  b: false,
  c: true,
  d: 0,
  e: 1,
  f: '',
  g: 'a',
  h: [null, false, '', true, 1, 'a'],
  i: { j: 0, k: false, l: 'a' }
};
compactObject(obj);
// { c: true, e: 1, g: 'a', h: [ true, 1, 'a' ], i: { l: 'a' } }
```

### deepClone

> 创建对象的深层克隆

```js
const deepClone = obj => {
  if (obj === null) return null;
  let clone = Object.assign({}, obj);
  Object.keys(clone).forEach(
    key =>
      (clone[key] =
        typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key])
  );
  if (Array.isArray(obj)) {
    clone.length = obj.length;
    return Array.from(clone);
  }
  return clone;
};

// Examples

const a = { foo: 'bar', obj: { a: 1, b: 2 } };
const b = deepClone(a); // a !== b, a.obj !== b.obj
```

