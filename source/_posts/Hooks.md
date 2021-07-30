---

layout: react
title: React Hooks
date: 2021-07-30 10:18:30
tags: ["React Hooks"]
categories: React
---

# React Hooks

Hook API 是React 自v16.8开始提供的，它使得函数组件也能实现与class组件相同的效果。

## useState

在v16.8之前，要想组件拥有自己的`state`就必须写成class组件：

````javascript
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  onClick = () => {
    this.setState({
      count: this.state.count + 1
    });
  };
  render() {
    return (
      <div className="App">
        <p>点击次数:{this.state.count}</p>
        <br />
        <button onClick={() => this.onClick()}>点击</button>
      </div>
    );
  }
}
````

而在v16.8开始，React提供了 `useState`使得函数组件也可以拥有自己的`state`：

````javascript
const App = () => {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(count + 1);
  };
  return (
    <div className="App">
      <p>点击次数:{count}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

<!-- more -->

在使用`useState`初始化数据时，也可以传入对象作为参数。但是当传入的参数是对象时，需要注意以下两点：

- 不可部分更新`state`

````javascript
const App = () => {
  const [fruits, setFruits] = useState({ apple: "苹果", banana: "香蕉" });
  const onClick = () => {
    setFruits({
      banana: "桔子"
    });
  };
  return (
    <div className="App">
      <p>{fruits.apple}</p>
      <p>{fruits.banana}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

在点击按钮之后将只显示"桔子"，这是`setState`不会合并属性，所以在更新`state`是要手动合并属性。上述代码可改写成：

````javascript
const App = () => {
  const [fruits, setFruits] = useState({ apple: "苹果", banana: "香蕉" });
  const onClick = () => {
    setFruits({
      ...fruits,
      banana: "桔子"
    });
  };
  return (
    <div className="App">
      <p>{fruits.apple}</p>
      <p>{fruits.banana}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

- 当传入的参数是对象，`setState(obj)`时obj的引用地址没有变化时，UI不会重新渲染。

````javascript
const App = () => {
  const [fruits, setFruits] = useState({ apple: "苹果", banana: "香蕉" });
  const onClick = () => {
    fruits.banana = "桔子"
    setFruits(fruits);//不会重新渲染
  };
  return (
    <div className="App">
      <p>{fruits.apple}</p>
      <p>{fruits.banana}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

## useEffect

`useEffect` 在每次render后都会执行，可以 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。如果存在多个`useEffect`，会按照出现顺序执行。Ajax请求可以放在`useEffect`中。`useEffect`的用法如下：

````javascript
const App = () => {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(count + 1);
  };
  useEffect(() => {
    console.log("每次render后执行");
  });
  return (
    <div className="App">
      <p>点击次数:{count}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

上面`useEffect`里的函数在每次render后都会执行一次。

````javascript
const App = () => {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(count + 1);
  };
  useEffect(() => {
    console.log("每次render后执行");
  });
  useEffect(() => {
    console.log("只在第一次render后执行");
  }, []);
  return (
    <div className="App">
      <p>点击次数:{count}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

在`useEffect`中传入第二个参数`[]`，可使得`useEffect`里的函数只在只在第一次render后执行，相当于 `componentDidMount`，即使`count`变化，这段代码也不会执行。

````javascript
const App = () => {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(count + 1);
  };
  useEffect(() => {
    console.log("每次render后执行");
  });
  useEffect(() => {
    console.log("只在第一次render后执行");
  }, []);
  useEffect(() => {
    console.log("count变化时执行");
  }, [count]);
  return (
    <div className="App">
      <p>点击次数:{count}</p>
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

在`useEffect`中传入第二个参数`[]`中传入所要依赖的变量，可使得`useEffect`里的函数在变量发生变化是执行。上述代码在每次`count`变化后都会执行(包括首次渲染)。

````javascript
const App = () => {
  useEffect(() => {
    const id = setInterval(() => {
      console.log("setInterval");
    }, 3000);
    return () => {
      window.clearInterval(id);
      console.log("清除setInterval");
    };
  }, []);
  const onClick = () => {
    ReactDOM.unmountComponentAtNode(document.getElementById("root"));
  };
  return (
    <div className="App">
      <button onClick={() => onClick()}>销毁组件</button>
    </div>
  );
};
````

需要清除的Effect的时候，在`useEffect`中返回一个函数，这个函数中进行一些清除的操作，组件在销毁的时候会执行其中的代码操作，相当于 `componentWillUnmount` 。

## useContext

useContext可以实现一个局部的全局变量，在这个范围内所有组件都能够访问。

````javascript
const Context = createContext(null);
const App = () => {
  const [count, setCount] = useState(0);
  return (
    <Context.Provider value={{ count, setCount }}>
      <div className="App">
        <p>App: {count}</p>
        <Child />
      </div>
    </Context.Provider>
  );
};

const Child = () => {
  const { count, setCount } = useContext(Context);
  const onClick = () => {
    setCount(count + 1);
  };
  return (
    <div>
      Child: {count}
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

首先使用`createContext`创建`Context`，然后使用`Context.Provider`圈定作用域，最后在作用域中使用`useContext(Context)`访问/操作变量。

## useRef

`useRef`返回一个对象，`useRef.current`的值就是传入的初始值。`useRef`有两种常用用法：

1.访问DOM

````javascript
const App = () => {
  const divRef = useRef(null);
  useEffect(() => {
    divRef.current.style.border = "1px solid red";
  });
  return (
    <div className="App" ref={divRef}>
      App
    </div>
  );
};
````

上述代码在`render`之后，改变`div`的`border`样式。

2.保存任意值

````javascript
const App = () => {
  const ref = useRef(0);
  useEffect(() => {
    console.log(ref.current);
  });
  return <div className="App">App</div>;
};
````

*注意：当`ref.current`的值发生改变时，组件不会重新渲染。

````javascript
const App = () => {
  const ref = useRef(0);
  const onClick = () => {
    ref.current = ref.current + 1;
    console.log(ref.current);
  };
  return (
    <div className="App">
      {ref.current}
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

解决方法：使用`useState`，在需要改变`ref.current`时进行`setState`。

````javascript
const App = () => {
  const ref = useRef(0);
  const [, setX] = useState(0);
  const onClick = () => {
    ref.current = ref.current + 1;
    console.log(ref.current);
    setX(Math.random() * 100);
  };
  return (
    <div className="App">
      {ref.current}
      <br />
      <button onClick={() => onClick()}>点击</button>
    </div>
  );
};
````

## useMemo

在理解`useMemo`之前，需要先了解`React.memo`。设想这样一个场景：`App`组件中有两个`state`，`App`组件自身使用一个`state x`，另外一个`state y`传递给子组件`Child`。我们的理解是，当`x`变化时，不会重新渲染子组件`Child`；只有当`y`改变时，才会重新渲染`Child`。

````javascript
const App = () => {
  console.log("运行App");
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const onClick = () => {
    setX(x + 1);
  };
  return (
    <div className="App">
      App:{x} <Child y={y}/>
      <br />
      <button onClick={() => onClick()}>change X</button>
    </div>
  );
};

function Child(props) {
  console.log("运行Child");
  return <span>Child: {props.y}</span>;
}
````

然而，实际上并不是如此，每当`x`变化时`Child`都会重新渲染。在这种情况下，可以使用`React.memo` 。`React.memo`会检查 `props `变更，如果`props`没有变化，则`Child`组件不会重新渲染。

````javascript
const App = () => {
  console.log("运行App");
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const onClick = () => {
    setX(x + 1);
  };
  return (
    <div className="App">
      App:{x} <Child2 y={y} />//使用Child2
      <br />
      <button onClick={() => onClick()}>change X</button>
    </div>
  );
};

function Child(props) {
  console.log("运行Child");
  return <span>Child: {props.y}</span>;
}

const Child2 = React.memo(Child);//新增代码
````

但是使用`React.memo`仍有不足之处：如果传递一个函数给`Child`组件，那么每当`x`变化时`Child`依然会重新渲染。

````javascript
const App = () => {
  console.log("运行App");
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const onClick = () => {
    setX(x + 1);
  };
  const onChildClick = ()=>{}
  return (
    <div className="App">
      App:{x} <Child2 y={y} fn={onChildClick}/>//传入函数
      <br />
      <button onClick={() => onClick()}>change X</button>
    </div>
  );
};

function Child(props) {
  console.log("运行Child");
  return <span onClick={props.fn}>Child: {props.y}</span>;//接收函数
}

const Child2 = React.memo(Child);
````

这是因为每当`x`变化时，`App`都会重新执行，那么`onChildClick`都会是一个新的引用，所以`React.memo`失效。

````javascript
const App = () => {
  console.log("运行App");
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const onClick = () => {
    setX(x + 1);
  };
  const onChildClick = useMemo(() => {
    return () => {};
  }, [y]);//缓存函数
  return (
    <div className="App">
      App:{x} <Child2 y={y} fn={onChildClick} />
      <br />
      <button onClick={() => onClick()}>change X</button>
    </div>
  );
};

function Child(props) {
  console.log("运行Child");
  return <span onClick={props.fn}>Child: {props.y}</span>;
}

const Child2 = React.memo(Child);
````

## useCallback

`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。那么上述`useMemo`的代码可改写成：

````javascript
const App = () => {
  console.log("运行App");
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const onClick = () => {
    setX(x + 1);
  };
  const onChildClick = useCallback(() => {}, [y]);//使用useCallback改写useMemo
  return (
    <div className="App">
      App:{x} <Child2 y={y} fn={onChildClick} />
      <br />
      <button onClick={() => onClick()}>change X</button>
    </div>
  );
};

function Child(props) {
  console.log("运行Child");
  return <span onClick={props.fn}>Child: {props.y}</span>;
}

const Child2 = React.memo(Child);
````



