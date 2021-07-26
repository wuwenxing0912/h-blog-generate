---
title: React生命周期
date: 2021-07-26 13:35:29
tags: ["React生命周期"]
categories: React
---

# 生命周期图解

React v16.3 之前的生命周期

| 初始化阶段         | 运行中阶段                | 销毁阶段             |
| ------------------ | ------------------------- | -------------------- |
| constructor        | componentWillUpdate       | componentWillUnmount |
| componentWillMount | render                    |                      |
| render             | componentDidUpdate        |                      |
| componentDidMount  | shouldComponentUpdate     |                      |
|                    | componentWillReceiveProps |                      |

{% asset_img old_lifecycle.jpg  旧生命周期图%}

现在的生命周期

| 初始化阶段               | 运行中阶段               | 销毁阶段             |
| ------------------------ | ------------------------ | -------------------- |
| constructor              | getDerivedStateFromProps | componentWillUnmount |
| getDerivedStateFromProps | render                   |                      |
| render                   | componentDidUpdate       |                      |
| componentDidMount        | shouldComponentUpdate    |                      |
|                          | getSnapshotBeforeUpdate  |                      |

{% asset_img new_lifecycle.jpg  新生命周期图%}

React v16.3之前的`componentWillMount`更名为`UNSAFE_componentWillMount`、`componentWillUpdate`更名为`UNSAFE_componentWillUpdate`、`componentWillReceiveProps`更名为`UNSAFE_componentWillReceiveProps`。且这些生命周期方法标记为“过时的"，这些方法仍然有效。



# 生命周期函数

下面代码是包含生命周期函数的[示例](https://codesandbox.io/s/practical-bush-gt5k6?file=/src/App.js)：

<!-- more -->

````javascript
import React from "react";
import ReactDOM from "react-dom";

class App extends React.Component {
  constructor(props) {
    super(props);
    console.log("创建 App");
    this.state = { count: 1 };
    /* {this.setState({ count: 2 })} */
  }

  UNSAFE_componentWillMount() {
    /* {this.setState({ count: 2 })} */
    console.log("将要 mount App");
  }

  componentDidMount() {
    console.log("已经 mount App");
    // this.setState({ count: 2 });
  }

  UNSAFE_componentWillUpdate() {
    // this.setState({ count: 2 });
    console.log("将要 update App");
  }

  componentDidUpdate() {
    console.log("已经 update App");
  }

  shouldComponentUpdate(nextProps, nextState) {
    console.log("shouldComponentUpdate");
    if (nextState.count < 3) {
      return true;
    } else {
      return false;
    }
  }

  componentWillUnmount() {
    console.log("将要 unmount App");
  }

  // static getDerivedStateFromProps() {
  //   /**如果使用此方法，
  //    * 那么这两个方法 UNSAFE_componentWillMount、UNSAFE_componentWillUpdate 不能同时使用
  //    */
  //   console.log("getDerivedStateFromProps");
  //   return null;
  // }

  onClick() {
    console.log("click");
    this.setState({ count: this.state.count + 1 });
  }

  unmount() {
    ReactDOM.unmountComponentAtNode(document.getElementById("root"));
  }

  render() {
    return (
      <div className="App">
        {this.state.count}
        {/* {this.setState({ count: 2 })} */}
        <br />
        <button onClick={() => this.onClick()}>点击加1</button>
        <button onClick={() => this.unmount()}>销毁组件</button>
        {console.log("render App")}
      </div>
    );
  }
}
````



## constructor

如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 `super(props)`。通常，在 React 中，构造函数仅用于以下两种情况：

  1.通过给 `this.state` 赋值对象来初始化[内部 state](https://react.docschina.org/docs/state-and-lifecycle.html)。

  2.为[事件处理函数](https://react.docschina.org/docs/handling-events.html)绑定实例。

## componentDidMount

`componentDidMount()` 会在组件挂载后立即调用，可以在 `componentDidMount()` 里直接调用 `setState()`。Ajax请求可以放在该生命周期内。

## componentDidUpdate

`componentDidUpdate()` 会在更新后会被立即调用。首次渲染不会执行此方法。

## componentWillUnmount

`componentWillUnmount()` 会在组件卸载及销毁之前直接调用。可以在此生命周期中清除定时器、取消网络请求等。

## shouldComponentUpdate

当 props 或 state 发生变化时，`shouldComponentUpdate()` 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 `forceUpdate()` 时不会调用该方法。如果 `shouldComponentUpdate()` 返回 `false`，则不会调用 `UNSAFE_componentWillUpdate()`，`render()` 和 `componentDidUpdate()`。



# 不能调用setState()方法的生命周期函数

- `constructor`

  在 `constructor()` 函数中不要调用 `setState()` 方法。如果在 `constructor()` 函数中调用 了`setState()` 方法，那么控制台会出现如下错误提示：`Can't call setState on a component that is not yet mounted.`。所以在组件挂载完成之前都不能调用`setState()` 方法。

- `UNSAFE_componentWillMount/componentWillMount`

- `render`

  如果在`render`内调用`setState()` 方法，`setState()` 方法会触发`render`，而`render`又将触发`setState()` ，进而出现无限循环。

- `UNSAFE_componentWillUpdate\componentWillUpdate`

  如果在`UNSAFE_componentWillUpdate\componentWillUpdate`内调用`setState()` 方法，当state更新时将触发`UNSAFE_componentWillUpdate\componentWillUpdate`，而`UNSAFE_componentWillUpdate\componentWillUpdate`又将触发`setState()` ，进而出现无限循环。

- `componentDidUpdate`

  如果在`componentDidUpdate`内调用`setState()` 方法，当state更新时将触发`componentDidUpdate`，而`componentDidUpdate`又将触发`setState()` ，进而出现无限循环。

