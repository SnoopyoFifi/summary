# JSBasic

[JavaScript开发者应懂的33个概念](https://github.com/stephentian/33-js-concepts)

## JS执行机制

- [浏览器是如何调度进程和线程的？](https://mp.weixin.qq.com/s/kSh600HxKYzb1ggtNZinTA)
- [js引擎的执行过程（一）](https://heyingye.github.io/2018/03/19/js%E5%BC%95%E6%93%8E%E7%9A%84%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B%EF%BC%88%E4%B8%80%EF%BC%89/)
- [JavaScript 是如何运行的？](https://blog.fundebug.com/2019/09/26/how-does-javascript-execute/)
- [这一次，彻底弄懂 JavaScript 执行机制](https://juejin.cn/post/6844903512845860872)

> js是单线程的，异步执行的，通过事件循环(Event Loop)的方式解析代码。

## 执行上下文(Execution Context)

- [Js 执行上下文和作用域](https://www.cnblogs.com/hai-cheng/p/11119632.html)
- [深入理解JavaScript执行上下文和执行栈](https://segmentfault.com/a/1190000018550118)

### 执行上下文

- 执行上下文是当前js代码被解析和执行时所在环境。
- js中运行的任何代码都是在执行上下文中运行的。
- 执行上下文决定哪段代码可以访问变量/函数/对象等。
- 执行上下文类型：
  - 全局执行上下文：
  - 函数执行上下文
  - `Eval`函数执行上下文

### 执行上下文栈

### 执行上下文的生命周期

> 执行上下文的生命周期包括三个阶段：创建阶段 -> 执行阶段 -> 回收阶段。

- 创建阶段：当函数被调用，但未执行任何其内部代码之前
  - 创建变量对象(`Variable Object`)·
  - 创建作用域链(`Scope Chain`)
  - 确定`this`指向
  
- 执行阶段：执行变量赋值，代码执行。
  - 变量赋值
  - 函数引用
  - 执行其他代码
- 回收阶段：执行上下文出栈等待虚拟机回收执行上下文。

## 作用域(Scope)

### 变量作用域

- 变量作用域是指代码中变量可以被访问和修改的有效范围。
- js采用词法作用域(Lexical scoping)，也就是静态作用域，函数的作用域在函数声明的时候就决定了。
- 作用域定义了一套规则用来管理引擎如何根据标识符来进行变量的查找，也就是确定当前执行代码对变量的访问权限。

### 作用域链(Scope Chain)

- [](https://blog.fundebug.com/2019/03/15/understand-javascript-scope/)
- [1](https://juejin.im/post/6844903660716032007)

全局作用域
局部作用域
块级作用域

变量的作用域，就是指变量可以被访问和修改的有效范围。
作用域链，就是指存在嵌套关系的作用域形成的变量查找范围。

### 变量的查找规则

### 变量提升

## 闭包

## `this`

### `this` 指向问题

### `call/apply/bind`

## 原型

## **参考文章和书籍**

- [深入理解 JavaScript 作用域和作用域链(https://blog.fundebug.com/2019/03/15/understand-javascript-scope/)·
