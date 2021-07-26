---
title: 实现一个基于React的Input组件
date: 2021-07-03 16:10:46
tags: ["React", "Input"]
categories: [React, TypeScript]
---

# API

本次编写的 [Input组件](https://wuwenxing0912.github.io/react-ui-lib/home.html#/input) 没有完全实现原生的 input 输入框，部分 Input  API 如下：

| API         | 说明                | 是否可选 | 默认值 | 类型                |
| ----------- | ------------------- | -------- | ------ | ------------------- |
| id          | 输入框的id          | 是       | ---    | string              |
| placeholder | 输入框的placeholder | 是       | ---    | string              |
| className   | 自定义类名          | 是       | ---    | string              |
| style       | 自定义样式          | 是       | ---    | React.CSSProperties |
| value       | 输入框的value       | 是       | ---    | string              |
| disabled    | 输入框禁用状态      | 是       | false  | boolean             |
| prefix      | 输入框前缀图标      | 是       | ---    | string              |
| suffix      | 输入框后缀图标      | 是       | ---    | string              |
| error       | 输入框后缀错误状态  | 是       | false  | boolean             |
| addOnBefore | 输入框前缀标签      | 是       | ---    | string              |
| addOnAfter  | 输入框后缀标签      | 是       | ---    | string              |

根据上述要求，那么就可以这样设计 Props ：

````typescript
interface Props extends React.InputHTMLAttributes<HTMLInputElement> {
	id?: string;
	placeholder?: string;
	className?: string;
	style?: React.CSSProperties;
	value?: string;
	disabled?: boolean;
	prefix?: string;
	suffix?: string;
	error?: boolean;
	addOnBefore?: string;
	addOnAfter?: string;
}
Input.defaultProps = {
	disabled: false,
	error: false,
};
````

<!-- more -->

# disabled

disabled 是可选参数，类型是 boolean，默认值为 false。当设置为 true 时，input 设置为 disabled，此时 input  为禁用状态。

````typescript
const Input: React.FunctionComponent<Props> = (props) => {
	const { disabled, ...rest } = props;
	return <input className={classes("x-input")} disabled={disabled} {...rest} />;
};
````

设置 disabled 代码如下：

````typescript
<Input disabled={true} placeholder="disabled"></Input>
````

效果如下：

{% asset_img disabled.jpg  disabled %}



# prefix & suffix

prefix/suffix 是可选参数，类型是 string。当传入 prefix/suffix 时，给 Input 嵌入 Icon 组件，显示对应的图标。

````typescript
const Input: React.FunctionComponent<Props> = (props) => {
	const { prefix, suffix, ...rest } = props;
	const prefixClassName = prefix && "prefix";
	const suffixClassName = suffix && "suffix";
     //classes 能够返回形如 x-input-wrapper prefix suffix 的类
	const inputWrapperClassName = classes("x-input-wrapper", prefixClassName, suffixClassName);
	return (
		<span className={inputWrapperClassName}>
			{prefix ? (
				<span className="prefix-wrapper">
					<Icon name={prefix} className="prefix-icon" />
				</span>
			) : ""}
			<input className={classes("x-input")} {...rest} />
			{suffix ? (
				<span className="suffix-wrapper">
					<Icon name={suffix} className="suffix-icon" />
				</span>
			) : ""}
		</span>
	);
};
````

设置 prefix/suffix 代码如下：

````typescript
<Input prefix="username" placeholder="username"></Input>
<Input suffix="password" placeholder="password"></Input>
````

效果如下：

{% asset_img pre-suf.jpg  prefix/suffix %}

# error

error 是可选参数，类型是 boolean，默认值为 false。当设置为 true 时，给 input 加上 error 类名。

````typescript
const Input: React.FunctionComponent<Props> = (props) => {
	const { error, ...rest } = props;
	const errorClassName = error ? "error" : "";
	return <input className={classes("x-input", errorClassName)} {...rest} />;
};
````

设置 error 代码如下：

````typescript
<Input error={true} placeholder="error"></Input>
````

效果如下：

{% asset_img error.jpg  error%}

# before & after

before/after 是可选参数，类型是 string。当传入 before/after  时，给 Input 添加前缀/后缀块。

````typescript
const Input: React.FunctionComponent<Props> = (props) => {
	const { addOnBefore, addOnAfter, ...rest } = props;
	const addOnAfterClassName = addOnAfter ? "after-text" : "";
	return (
		<span>
			{addOnBefore ? <span className="before-text">{addOnBefore}</span> : ""}
			<input className={classes("x-input", addOnAfterClassName)} {...rest} />
			{addOnAfter ? <span className="after-text">{addOnAfter}</span> : ""}
		</span>
	);
};
````

设置 before/after 代码如下：

````typescript
<Input addOnBefore="Before" addOnAfter="After" placeholder="placeholder"></Input>
<Input addOnBefore="Before" placeholder="placeholder"></Input>
<Input addOnAfter="After" placeholder="placeholder"></Input>
````

效果如下：

{% asset_img before-after.jpg  before/after %}
