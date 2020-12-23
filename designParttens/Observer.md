# 观察者模式(`Observer Partten`)

## 概述

> 口诀：**主体对象、观察者**

- 定义：模块是指一个拥有公共接口的封闭命名空间。
- 应用场景：创建对象时，需要进行内部初始化，同时对内部属性和方法有访问权限限制，就需要使用模块模式。
- 实现思路：利用 闭包（作用域链）原理实现私有变量，保护模块内部变量无法从模块外部修改。

## 具体实现

```js
  const Event = (function() {
    let clientList = {};
    let listen = null;
    let trigger = null;
    let remove = null;

    listen = function(key, fn) {
      const fns = clientList[key];
      if (!fns) {
        clientList[key] = [];
      }
      clientList[key].push(fn);
    };

    trigger = function(key, message) {
      const fns = clientList[key];
      if (!fns || fns.length === 0) {
        return false;
      }
      for(let  i = 0, len =fns.length; i < len; i++) {
        const fn = fns[i];
        fn(message);
      }
    }

    remove = function(key, fn) {
      const fns = clientList[key];
      if (!fns) {
        return false;
      }
      if (!fn) {
        clientList[key] = [];
      } else {
        for(let i = 0, len = fns.length; i < len; i++) {
          const _fn = fns[i];
          if (_fn === fn) {
            fns.splice(i, 1);
          }
        }
      }
    }

    return {
      listen,
      trigger,
      remove,
    }
  })();

  // 订阅
  Event.listen('say', (message) => {
    console.log('订阅者收到信息: ' + message)
  })
  // 发布
  Event.trigger('say', '--> test say message')
```

## 具体应用

> 

## **参考文章和书籍**

- [JS 设计模式](https://set.sh/post/180612-js-design-pattern)
