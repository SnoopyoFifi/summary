# 观察者模式(`Observer Partten`)

## 概述

> 口诀：**主体对象、观察者**

- 定义：观察者模式，它定义了一种一对多的关系，让多个观察者对象同时监听某一个主题对象，这个主题对象的状态发生变化时就会通知所有的观察者对象，使得它们能够自动更新自己
- 应用场景：一个对象的行为依赖于另一个对象的状态。或者换一种说法，当被观察对象（目标对象）的状态发生改变时，会直接影响到观察对象的行为。
- 实现思路：

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


interface Observer {
  notify: Function
}

class ConcreteObserver implements Observer {
  constructor(private name) {};

  notify() {
    console.log(`${this.name} has been notified`);
  };
}

class Subject {
  private observers = [];

  public addObserver(observer) {
    console.log(observer, 'is pushed!');
    this.observers.push(observer);
  }

  public deleteObserver(observer) {
    console.log('remove', observer);
    const n = this.observers.indexOf(observer);
    n != -1 && this.observers.splice(n, 1);
  }

  public notifyObservers() {
    console.log('notify all the observers', this.observers);
    this.observers.forEach(observer => observer.notify());
  }
}

const subject = new Subject();
const xiaoQin = new ConcreteObserver("小秦");
const xiaoWang = new ConcreteObserver("小王");
subject.addObserver(xiaoQin);
subject.addObserver(xiaoWang);
subject.notifyObservers();

subject.deleteObserver(xiaoQin);
subject.notifyObservers();
```

## 具体应用

> 

## **参考文章和书籍**

- [JS 设计模式](https://set.sh/post/180612-js-design-pattern)
