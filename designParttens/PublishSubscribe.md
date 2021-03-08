# 观察者模式(`Observer Partten`)

## 概述

> 口诀：**发布者、topic/event channel、订阅者**

- 定义：在软件架构中，发布/订阅是一种消息范式，消息的发送者（称为发布者）不会将消息直接发送给特定的接收者（称为订阅者）。而是将发布的消息分为不同的类别，然后分别发送给不同的订阅者。同样的，订阅者可以表达对一个或多个类别的兴趣，只接收感兴趣的消息，无需了解哪些发布者存在。
- 应用场景：对象间存在一对多关系，一个对象的状态发生改变会影响其他对象。作为事件总线，来实现不同组件间或模块间的通信。
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


class EventEmitter {
  private c = new Map();

  // 订阅指定的主题
  subscribe(topic, ...hanlers) {
    let topics = this.c.get(topic);
    if (!topics) {
      this.c.set(topic, topics = []);
    }
    topics.push(...handlers);
  }

  // 取消订阅指定的主题
  unsubscribe(topic, handlers) {
    if (!handler) {
      return this.c.delete(topic);
    }

    const topics = this.c.get(topic);
    if (!topics) {
      return false;
    }

    const index = topics.indexOf(hanlder);

    if (index < 0) {
      return false;
    }
    topics.splices(index, 1);
    if (topics.length === 0) {
      this.c.delete(topic);
    }
    return true;
  }

  // 为指定的主题发布消息
  publish(topic, ...args) {
    const topics = this.c.get(topic);
    if(!topic) {
      return null;
    }
    return topics.map(handler => {
      try {
        return handler(...args);
      } catch(e) {
        console.error(e);
        return null;
      }
    })
  }
}

const eventEmitter = new EventEmitter();
eventEmitter.subscribe("ts", (msg) =>console.log(`收到订阅的消息：${msg}`) );
eventEmitter.publish("ts", "TypeScript发布订阅模式");
eventEmitter.unsubscribe("ts");eventEmitter.publish("ts", "TypeScript发布订阅模式");
```

## 具体应用

  > 

## **参考文章和书籍**

- [JS 设计模式 - 观察者模式](https://set.sh/post/180612-js-design-pattern#观察者模式)
