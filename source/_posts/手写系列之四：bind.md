---
title: 手写系列之四：bind
date: 2021-07-20 20:31:59
tags: ["bind", "手写系列"]
categories: JavaScript
---

# bind

根据[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) bind的定义：`bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。用代码说明一下上述的定义：

````javascript
const person1 = {
  name: "AA",
  getName: function() {
    return this.name;
  }
};

const person2 = {
  name: "BB",
  getName: function() {
    return this.name;
  }
};

//使用bind时，会创建新函数且返回它，第一个参数作为新函数的this
const p1 = person1.getName.bind(person2);

person1.getName(); //"AA"
p1(); //"BB"
````

接下来就开始着手用代码模拟bind。

# 测试代码

和上一篇文章一样，首先给上测试代码，依据测试代码完成[模拟bind](https://github.com/wuwenxing0912/hand-writing/tree/master/bind)。

<!-- more -->

````typescript
import * as chai from "chai";
import bind from "../src/index";

const assert = chai.assert;

describe("bind", () => {
  it("1.bind 是函数", () => {
    //@ts-ignore
    Function.prototype.bind2 = bind;
    //@ts-ignore
    assert.isFunction(Function.prototype.bind2);
  });

  it("2.能够绑定 this", () => {
    //@ts-ignore
    Function.prototype.bind2 = bind;
    const f1 = function () {
      return this;
    };
    //@ts-ignore
    const f2 = f1.bind2({ name: "bind" });
    assert.equal(f2().name, "bind");
});

  it("3.绑定 this, 接收参数 p1, p2", () => {
    //@ts-ignore
    Function.prototype.bind2 = bind;
    const f1 = function (p1, p2) {
      return [this, p1, p2];
    };
    //@ts-ignore
    const f2 = f1.bind2({ name: "bind" }, "x", "y");
    assert.equal(f2()[0].name, "bind");
    assert.equal(f2()[1], "x");
    assert.equal(f2()[2], "y");
  });

  it("4.绑定 this, 首先接收参数 p1, 后接收参数p2", () => {
    //@ts-ignore
    Function.prototype.bind2 = bind;
    const f1 = function (p1, p2) {
      return [this, p1, p2];
    };
    //@ts-ignore
    const f2 = f1.bind2({ name: "bind" }, "x");
    assert.equal(f2("y")[0].name, "bind");
    assert.equal(f2("y")[1], "x");
    assert.equal(f2("y")[2], "y");
  });

  it("5.能够使用 new", () => {
    //@ts-ignore
    Function.prototype.bind2 = bind;
    const f1 = function (p1, p2) {
      this.p1 = p1;
      this.p2 = p2;
    };
    //@ts-ignore
    const f2 = f1.bind2(undefined, "x", "y");
    const obj = new f2();
    assert.equal(obj.p1, "x");
    assert.equal(obj.p2, "y");
  });

  it("6.new 的时候绑定了 p1, p2，并且在 f1 的 prototype 上添加方法", () => {
    //@ts-ignore
    Function.prototype.bind2 = bind;
    const f1 = function (p1, p2) {
      this.p1 = p1;
      this.p2 = p2;
    };
    f1.prototype.method = function () {};
    //@ts-ignore
    const f2 = f1.bind2(undefined, "x", "y");
    const obj = new f2();
    assert.equal(obj.p1, "x");
    assert.equal(obj.p2, "y");
    assert.isTrue(f1.prototype.isPrototypeOf(obj));
    assert.isFunction(obj.method);
  });
});
````

# 实现过程

- 1.bind 是函数

  ````typescript
  function bind() {}
  ````

- 2.能够绑定 this

  ````typescript
  function bind(asThis, ...args) {
    const fn = this;
    if (typeof fn !== "function") {
      throw new Error("请使用函数调用bind");
    }
    return function (...args2) {
      return fn.call(asThis, ...args, ...args2);
    };
  }
  ````

- 3.绑定 this, 接收参数 p1, p2

  ````typescript
  function bind(asThis, ...args) {
    const fn = this;
    if (typeof fn !== "function") {
      throw new Error("请使用函数调用bind");
    }
    return function (...args2) {
      return fn.call(asThis, ...args, ...args2);
    };
  }
  ````

- 4.绑定 this, 首先接收参数 p1, 后接收参数p2

  ````typescript
  function bind(asThis, ...args) {
    const fn = this;
    if (typeof fn !== "function") {
      throw new Error("请使用函数调用bind");
    }
    return function (...args2) {
      return fn.call(asThis, ...args, ...args2);
    };
  }
  ````

- 5.能够使用 new

  ````typescript
  function bind(asThis, ...args) {
    const fn = this;
    if (typeof fn !== "function") {
      throw new Error("请使用函数调用bind");
    }
    return function result(...args2) {
      return fn.call(this instanceof result ? this : asThis, ...args, ...args2);
    };
  }
  ````

- 6.new 的时候绑定了 p1, p2，并且在 f1 的 prototype 上添加 sayHi

  ````typescript
  function bind(asThis, ...args) {
    const fn = this;
    if (typeof fn !== "function") {
      throw new Error("请使用函数调用bind");
    }
    function result(...args2) {
      return fn.call(this instanceof result ? this : asThis, ...args, ...args2);
    }
    result.prototype = Object.create(fn.prototype);
    return result;
  }
  ````

  
