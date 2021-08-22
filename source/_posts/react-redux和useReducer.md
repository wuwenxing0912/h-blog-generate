---
title: Redux和useReducer
date: 2021-08-22 12:52:06
tags: ["Redux", "useReducer"]
categories: [React, Redux]
---

# Redux

Redux 是 JavaScript 应用的状态容器，提供可预测的状态管理。简单来说就是可以通过使用 Redux 管理 JavaScript 应用的数据。

下面使用 Redux 完成一个[计数器小例子](https://codesandbox.io/s/practical-sun-k29jf?file=/index.html)：

## Store

Store 就是保存数据的地方，它是一个对象。使用 `Redux.createStore` 创建 store，`Redux.createStore` 接受一个函数(reducer)作为参数。

````javascript
const store = Redux.createStore(reducer);
````

## State

State 就是存放在 Store 中的数据，可通过 `store.getState()`来获取 State。

````html
<div id="app"></div>
````

````javascript
function render() {
  document.getElementById("app").innerHTML = `
  点击次数：<span id="value">${store.getState()}</span>
  <div>
    <button onclick="add()">+1</button>
    <button onclick="subs()">-1</button>
  </div>`;
}
````

## Reducer

Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。

````javascript
function reducer (state, action) {
  if (typeof state === "undefined") {
    return 0;
  }
  switch (action.type) {
    case "add":
      return state + action.playload;
    case "subs":
      return state - action.playload;
    default:
      return state;
  }
}
````

## Action 

Action 是操作数据的动作，它是一个对象。Action 中操作数据的动作是事先约定好的。

````javascript
//action1
{
    type: "add",
    playload: 1
}

//action2
{
    type: "subs",
    playload: 1
}
````

## Dispatch

store 中有一个方法：`dispatch`，它是更新 state 的唯一方式。它接受一个 Action 对象作为参数。

````javascript
function add() {
  store.dispatch({ type: "add", playload: 1 });
}

function subs() {
  store.dispatch({ type: "subs", playload: 1 });
}
````

## Subscribe

当 state 发生变化时，需要监听它的变化并及时更新UI。store 中有一个方法：`subscribe`，它可满足上述要求。

````javascript
store.subscribe(render);
````

至此，[计数器例子](https://codesandbox.io/s/practical-sun-k29jf?file=/index.html)已经完成，下面给出完整代码。

<!-- more -->

````html
<div id="app"></div>
````

````javascript
function render() {
  document.getElementById("app").innerHTML = `
  点击次数：<span id="value">${store.getState()}</span>
  <div>
    <button onclick="add()">+1</button>
    <button onclick="subs()">-1</button>
  </div>`;
}

function reducer(state, action) {
  if (typeof state === "undefined") {
    return 0;
  }
  switch (action.type) {
    case "add":
      return state + action.playload;
    case "subs":
      return state - action.playload;
    default:
      return state;
  }
}

const store = Redux.createStore(reducer);
render();
store.subscribe(render);

function add() {
  store.dispatch({ type: "add", playload: 1 });
}

function subs() {
  store.dispatch({ type: "subs", playload: 1 });
}
````

# 在 React 中使用 Redux

前一个例子是在原生 JavaScript 中使用 Redux，现在在 React 框架里使用 Redux。[代码如下](https://codesandbox.io/s/nostalgic-sky-frejf?file=/src/index.js)：

````javascript
//index.js
import { StrictMode } from "react";
import ReactDOM from "react-dom";
import { createStore } from "redux";

import App from "./App";

function reducer(state, action) {
  if (typeof state === "undefined") {
    return 0;
  }
  switch (action.type) {
    case "add":
      return state + action.playload;
    case "subs":
      return state - action.playload;
    default:
      return state;
  }
}

function render() {
  ReactDOM.render(
    <StrictMode>
      <App value={store.getState()} add={add} subs={subs} />
    </StrictMode>,
    document.getElementById("root")
  );
}
const store = createStore(reducer);
render();
store.subscribe(render);

function add() {
  store.dispatch({ type: "add", playload: 1 });
}

function subs() {
  store.dispatch({ type: "subs", playload: 1 });
}
````

````javascript
//App.js
export default function App(props) {
  return (
    <div>
      点击次数：<span>{props.value}</span>
      <div>
        <button onClick={() => props.add()}>+1</button>
        <button onClick={() => props.subs()}>-1</button>
      </div>
    </div>
  );
}
````

但是，在 React框架里可能遇到这样一个问题：App 组件中有若干个子组件，子组件中也有若干个子组件，那么就需要把 store 一层一层地传递下去。这是非常繁琐的一个做法。而现在可以用 useReducer 代替 Redux 实现这样的需求。

# useReducer

在代替 Redux 之前，使用 useReducer 完成一个单组件[计数器小例子](https://codesandbox.io/s/nice-brattain-zezfr?file=/src/index.js)

````javascript
import React, { useReducer } from "react";
import ReactDOM from "react-dom";

const initState = {
  n: 0
};

function reducer(state, action) {
  switch (action.type) {
    case "add":
      return { n: state.n + action.playload };
    case "subs":
      return { n: state.n - action.playload };
    default:
      return state;
  }
}

function App() {
  const [state, dispatch] = useReducer(reducer, initState);
  const add = () => dispatch({ type: "add", playload: 1 });
  const subs = () => dispatch({ type: "subs", playload: 1 });
  return (
    <div>
      点击次数：<span>{state.n}</span>
      <div>
        <button onClick={add}>+1</button>
        <button onClick={subs}>-1</button>
      </div>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
````

# 用 useReducer 代替 Redux

[使用 useReducer 代替 Redux](https://codesandbox.io/s/frosty-resonance-kircf?file=/src/App.js) 可分为以下几个步骤：

1.把数据放在 store 对象中：

````javascript
const store = {
  n: 0
};
````

2.把所有操作放在 reducer 中

````javascript
function reducer(state, action) {
  switch (action.type) {
    case "add":
      return { n: state.n + action.playload };
    case "subs":
      return { n: state.n - action.playload };
    default:
      return state;
  }
}
````

3.创建Context

````javascript
const Context = createContext(null);
````

4.使用 useReducer 得到对 store 读写的 state, dispatch

````javascript
const [state, dispatch] = useReducer(reducer, store);
````

5.使用 Context.Provider 并传入 state, dispatch

````javascript
<Context.Provider value={{ state, dispatch }}>
</Context.Provider>
````

6.把 Context 提供给所有组件

````javascript
<Context.Provider value={{ state, dispatch }}>
   点击次数：{state.n} <br />
   <AddOne />
   <AddTwo />
   <SubsOne />
   <AddThree />
</Context.Provider>
````

7.各组件使用 useContext 获取 state, dispatch

````javascript
//addOne.js
import { useContext } from "react";
import Context from "./contetx";
function AddOne() {
  const { state, dispatch } = useContext(Context);
  const addOne = () => dispatch({ type: "add", playload: 1 });
  return <button onClick={addOne}>+1</button>;
}
````

按照上述步骤，凡是被 `Context.Provider` 包裹的，都可以对 store 读写。

