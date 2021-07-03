---
title: 实现一个基于React的Button组件
date: 2021-06-20 17:18:44
tags: ["React", "Button"]
categories: [JavaScript, TypeScript]
---

# API

在开始编写 [Button组件](https://wuwenxing0912.github.io/react-ui-lib/home.html) 之前，首先构思 Button 需要提供的 API，然后按照 API 的要求着手实现。API 具体如下：

| API        | 说明                     | 默认值  | 类型                                           |
| ---------- | ------------------------ | ------- | ---------------------------------------------- |
| type       | button类型               | default | "default" \| "primary" \| "dashed" \| "danger" |
| className  | 自定义类名               | ---     | string                                         |
| style      | 自定义样式               | ---     | React.CSSProperties                            |
| disabled   | 按钮禁用状态             | false   | boolean                                        |
| ghost      | 幽灵按钮，背景透明       | false   | boolean                                        |
| size       | 按钮尺寸大小             | middle  | "large" \| "middle" \| "small"                 |
| icon       | 自定义按钮图标           | ---     | string                                         |
| onClick    | 点击按钮时的回调         | ---     | React.MouseEventHandler                        |
| buttonType | 设置原生button的type属性 | button  | "button" \|"submit" \|"reset"                  |

根据上述要求，那么就可以这样设计 Props ：

````typescript
interface Props {
	type?: "default" | "primary" | "dashed" | "danger";
	className?: string;
	style?: React.CSSProperties;
	disabled?: boolean;
	ghost?: boolean;
	size?: "large" | "middle" | "small";
	icon?: string;
	onClick?: React.MouseEventHandler;
	buttonType?: "button" | "submit" | "reset"
}

Button.defaultProps = {
	type: "default",
	disabled: false,
	ghost: false,
	size: "middle",
	buttonType: "button"
};
````

<!-- more -->

# type

button 共有4种类型分别为：default(默认按钮)、primary(主题按钮)、dashed(虚线按钮)和danger(危险按钮)。type 为可选参数，默认值为 default。每当传入 type 时，就相应的给 button 加上对应的类名，根据不同的类 button 的显示不同的样式。

````typescript
const Button: React.FunctionComponent<Props> = (props) => {
	const { type, ...rest } = props;
	const buttonClassName = classes("x-button", type); //classes 能够返回形如 x-button type 的类
	return (
		<Fragment>
		    <button className={buttonClassName} {...rest}></button>
		</Fragment>
	);
};
````

设置 button type 代码如下：

````typescript
<Button type="default">Default</Button>
<Button type="primary">Primary</Button>
<Button type="dashed">Dashed</Button>
<Button type="danger">Danger</Button>
````

效果如下：

{% asset_img type.jpg  type %}

# disabled

disabled 是可选参数，类型是 boolean，默认值为 false。当设置为 true 时，给 button 加上 disabled 类名，此时 button 为禁用状态。

````typescript
const Button: React.FunctionComponent<Props> = (props) => {
	const { type, ...rest } = props;
	const disabled = props.disabled ? "disabled" : undefined;//disabled 为 true 时，加上 disabled 类
	const buttonClassName = classes("x-button", type, disabled);
	return (
		<Fragment>
		    <button className={buttonClassName} {...rest}></button>
		</Fragment>
	);
};
````

设置 disabled 代码如下：

````typescript
<Button type="default" disabled={true}>Default</Button>
<Button type="primary" disabled={true}>Primary</Button>
<Button type="dashed" disabled={true}>Dashed</Button>
<Button type="danger" disabled={true}>Danger</Button>
````

效果如下：

{% asset_img disabled.jpg  disabled %}

# ghost

ghost 是可选参数，类型是 boolean，默认值为 false。当设置为 true 时，给 button 加上 ghost 类名，此时 button 背景变为透明。

````typescript
const Button: React.FunctionComponent<Props> = (props) => {
	const { type, ...rest } = props;
	const ghost = props.ghost ? "ghost" : undefined;
	const buttonClassName = classes("x-button", type, ghost);
	return (
		<Fragment>
		    <button className={buttonClassName} {...rest}></button>
		</Fragment>
	);
};
````

设置 ghost 代码如下：

````typescript
<Button type="default" ghost={true}>Default</Button>
<Button type="primary" ghost={true}>Primary</Button>
<Button type="dashed" ghost={true}>Dashed</Button>
<Button type="danger" ghost={true}>Danger</Button>
````

效果如下：

{% asset_img ghost.jpg  ghost%}

# size

size 共有3种分别为：large(大)、middle(中)和small(小)。size为可选参数，默认值为 middle。每当传入 size 时，就相应的给 button 加上对应的类名，根据不同的类 button 的显示不同的样式。

````typescript
const Button: React.FunctionComponent<Props> = (props) => {
	const { type, ...rest } = props;
	const size = props.size ? props.size : "middle";
	const onClick = (e: React.MouseEvent) => {
		if (disabled) return e.preventDefault(); //当 disabled 为 true 时， 阻止点击事件。
		props.onClick && props.onClick();
	};
	const buttonClassName = classes("x-button", type, size);
	return (
		<Fragment>
		    <button className={buttonClassName} onClick={onClick} {...rest}></button>
		</Fragment>
	);
};
````

设置 size 代码如下：

````typescript
const [size, setSize] = useState("middle"); //初始化 size 为 middle
<Button type="default" onClick={() => {setSize("large")}}>Default</Button> //点击时改变 size 的值为 large
<Button type="primary" onClick={() => {setSize("middle")}}>Primary</Button> //点击时改变 size 的值为 middle
<Button type="dashed" onClick={() => {setSize("middle")}}>Dashed</Button> //点击时改变 size 的值为 middle
<Button type="danger" onClick={() => {setSize("small")}}>Danger</Button> //点击时改变 size 的值为 small
````

效果如下：

{% asset_img size.jpg  size%}

# icon

icon 是可选参数，类型是 string。

````typescript
const Button: React.FunctionComponent<Props> = (props) => {
	const { type, ...rest } = props;
	const icon = props.icon && <Icon name={props.icon}></Icon>;//使用 Icon 组件，name 匹配 icon 库中的icon
	const buttonClassName = classes("x-button", type);
	return (
		<Fragment>
		    <button className={buttonClassName} {...rest}>
			    {icon}
		    </button>
		</Fragment>
	);
};
````

设置 icon 代码如下：

````typescript
<Button type="default" icon="modify">Default</Button>
<Button type="primary" icon="modify">Primary</Button>
<Button type="dashed" icon="modify">Dashed</Button>
<Button type="danger" icon="modify">Danger</Button>
````

效果如下：

{% asset_img icon.jpg  icon%}
