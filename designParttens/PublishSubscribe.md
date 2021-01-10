# 观察者模式(`Observer Partten`)

## 概述

  > 口诀：**发布者、topic/event channel、订阅者**

- 定义：
- 应用场景：
- 实现思路：发布/订阅模式是观察者模式的变种。其实现了一个主体/事件通道，用于发布者和订阅者之间的通信，以解耦发布者和订阅者。

## 具体实现

  ```js
class Pub {
  constructor() {
    this.caches = [];

    Reflect.defineProperty(this, '_uid', {
      enumerable: false,
      writable: true,
      value: -1,
    })
  }

  publish(evt, ...args) {
    if(!this.caches[evt]) return false;

    const subscription = this.caches[evt];
    let len = subscribers ? subscribers.length : 0;
    while(len--) {
      subscribers[len].callback.apply(subscribers[len], args);
    }

    return this;
  }

  subscribe(evt, callback) {
    if(!this.caches[evt]) this.caches[evt] = [];

    this.caches[evt].push({
      uid: ++this._uid,
      callback
    })
  }

  unsubscribe(evt, uid) {
    const cache = this.caches[evt];
    const len = this.caches[evt].length;

    for(let i = 0; i < len; i++) {
      if (cache[i].uid === uid) {
        return cache[i].splice(i, 1);
      }
    }

    return this;
  }
}

const pub = new Pub();

pub.subscribe('snoopy', () => console.log('snoopy say hi'));
pub.subscribe('fifi', () => console.log('fifi say hi'));

pub.publish('snoopy');
pub.publish('fifi');

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


// 观察者缓存容器（列表）
class ObserverList {
  constructor() {
    this.observerList = [];
  }

  add(observer) {
    return this.observerList.push(observer);
  }

  remove(index) {
    return this.observerList.splice(index, 1);
  }

  get(index) {
    return this.observerList[index];
  }

  len() {
    return this.observerList.length;
  }

  indexOf(observer, startIndex = 0) {
    const len = this.observerList.length;
    for(let  i = startIndex; i < len; i++) {
      if (this.observerList[i] === observer) {
        return i;
      }
    }
    return -1;
  }
}

// 主体
class Subject {
  constructor() {
    this.observers = new ObserverList();
  }

  addObserver(observer) {
    this.observers.add(observer);
  }

  removeObserver(observer) {
    const index = this.observers.indexOf(observer, 0);
    return this.observers.remove(index);
  }

  notify(context) {
    const len = this.observers.len();
    for (let i = 0; i < len; i++) {
      this.observers.get(i).update(context);
    }
  }
}

// 观察者
class Observer {
  update(context) {
    console.log('observer has been updated.');
  }
}

// 实例
const sub = new Subject();
const obs = new Observer();

sub.addObserver(obs);

sub.notify();
sub.removeObserver(obs);
  ```jssss

## 具体应用

  > 

## **参考文章和书籍**

- [JS 设计模式 - 观察者模式](https://set.sh/post/180612-js-design-pattern#观察者模式)
