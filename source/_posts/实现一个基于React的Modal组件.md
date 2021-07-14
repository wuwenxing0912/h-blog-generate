---
title: 实现一个基于React的Modal组件
date: 2021-07-12 18:45:46
tags: ["React", "Modal"]
categories: [JavaScript, TypeScript]
---

# API

[Modal 组件](https://wuwenxing0912.github.io/react-ui-lib/home.html#/modal)包含对话框和遮罩层。API 具体如下：

| API               | 说明                      | 默认值   | 是否可选 | 类型                                           |
| ----------------- | ------------------------- | -------- | -------- | ---------------------------------------------- |
| visible           | Modal 是否可见            | ---      | 否       | boolean                                        |
| onClose           | 关闭 Modal 回调函数       | ---      | 否       | React.MouseEventHandler                        |
| onConfirm         | 确定 Modal 回调函数       | ---      | 否       | React.MouseEventHandler                        |
| onClickMaskClose  | 是否点击遮罩层关闭 Modal  | false    | 是       | boolean                                        |
| title             | Modal 标题                | "对话框" | 是       | string \| ReactNode                            |
| content           | Modal 内容                | ---      | 是       | string \| ReactNode                            |
| cancelText        | Modal 取消按钮 内容       | 取消     | 是       | string                                         |
| confirmText       | Modal 确定按钮 内容       | 确定     | 是       | string                                         |
| ancelButtonType   | Modal 取消按钮 类型       | default  | 是       | "default" \| "primary" \| "dashed" \| "danger" |
| confirmButtonType | Modal 确定按钮 类型       | primary  | 是       | "default" \| "primary" \| "dashed" \| "danger" |
| className         | 自定义 Modal 类名         | ---      | 是       | string                                         |
| style             | 自定义 Modal外层容器 样式 | ---      | 是       | React.CSSProperties                            |

根据上述要求，那么就可以这样设计 Props ：

````typescript
interface Props {
	visible: boolean;
	onClose: React.MouseEventHandler;
	onConfirm: React.MouseEventHandler;
	onClickMaskClose?: boolean;
	title?: string | ReactNode;
	content?: string | ReactNode;
	cancelText?: string;
	confirmText?: string;
	cancelButtonType?: "default" | "primary" | "dashed" | "danger";
	confirmButtonType?: "default" | "primary" | "dashed" | "danger";
}

Modal.defaultProps = {
	onClickMaskClose: false,
	title: "对话框",
	cancelText: "取消",
	confirmText: "确定",
	cancelButtonType: "default",
	confirmButtonType: "primary",
};
````

<!-- more -->

# visible

visible是控制Modal显示/隐藏的参数，且visible必传参数，类型是boolean。

# onClose & onConfirm

onClose和onConfirm分别是关闭Modal的回调函数以及确定Modal的回调函数，类型均为React.MouseEventHandler，且为必传参数。

# onClickMaskClose

onClickMaskClose是可选参数，类型是boolean，默认值为false。当设置为true时，点击遮罩层时，Modal关闭。

# title & content

title和content分别是Modal的标题以及Modal的内容，类型均为string | ReactNode，为可选参数。title的默认值为 "对话框"。

# cancelText & confirmText

cancelText和confirmText分别是Modal取消按钮的内容以及Modal确定按钮的内容，类型均为string ，为可选参数，默认值分别为"取消"和"确定"。

# cancelButtonType & confirmButtonType

cancelButtonType和confirmButtonType分别是Modal取消按钮的类型以及Modal确定按钮的类型，类型均为 "default" | "primary" | "dashed" | "danger" ，为可选参数，默认值分别为"default"和"primary"。

# 完整代码

````typescript
const portal = props.visible ? (
  <Fragment>
    <div className={classes("x-modal-mask")} onClick={onClickMaskClose}></div>
    <div className={classes("x-modal")}>
      <header className={classes("x-modal-header")}>
        <div className={classes("x-modal-header-content")}>{props.title}</div>
        <div
          className={classes("x-modal-icon-wrapper")}
          onClick={props.onClose}
        >
          <Icon name="close" className={classes("x-modal-icon")}></Icon>
        </div>
      </header>
      <main className={classes("x-modal-main")}>{props.content}</main>
      <footer className={classes("x-modal-footer")}>
        <Button
          type={props.cancelButtonType}
          className={classes("x-modal-footer-button")}
          onClick={props.onClose}
        >
          {props.cancelText}
        </Button>
        <Button type={props.confirmButtonType} onClick={props.onConfirm}>
          {props.confirmText}
        </Button>
      </footer>
    </div>
  </Fragment>
) : null;
return ReactDOM.createPortal(portal, document.body);
};
````

