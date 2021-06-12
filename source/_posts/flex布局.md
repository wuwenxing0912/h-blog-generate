---
title: flex布局
date: 2018-07-11 16:26:50
tags: flex
categories: CSS
---

# 一、flex布局
flex布局（Flexible Box）意为"弹性布局"，可以实现空间的自动分配，自动对齐。
使用flex布局的元素，称为flex容器（flex container），它的所有子元素自动成为容器成员，称为flex item。
容器默认存在两根轴：水平的主轴（main axis）和垂直的侧轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；侧轴的开始位置叫做cross start，结束位置叫做cross end。
<!-- more -->
{% asset_img flex.jpg flex %}

# 二、容器属性
容器上有6个属性：flex-direction、flex-wrap、flex-flow、justify-content、align-items、align-content。

## 1、flex-direction属性确定主轴的方向，flex-direction属性有4个值可选：
flex-direction：row; 主轴为水平方向，起点在左端（默认值）
{% asset_img row.jpg row %}

flex-direction：row-reverse; 主轴为水平方向，起点在右端。
{% asset_img row-reverse.jpg row-reverse %}

flex-direction：column; 主轴为垂直方向，起点在上方。
{% asset_img column.jpg column %}

flex-direction：column-reverse; 主轴为垂直方向，起点在下方。
{% asset_img column-reverse.jpg column-reverse %}

## 2、flex-wrap属性确定是否换行，flex-wrap属性有3个值可选：
flex-wrap：nowrap; 不换行（默认值）
{% asset_img nowrap.jpg nowrap %}

flex-wrap：wrap; 换行，第一行在上方。
{% asset_img wrap.jpg wrap %}

flex-wrap：wrap-reverse; 换行，第一行在下方。
{% asset_img wrap-reverse.jpg wrap-reverse %}

## 3、flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
flex-flow：row wrap; 
{% asset_img flex-flow.jpg flex-flow %}

## 4、justify-content属性定义了item在主轴上的对齐方式。justify-content属性有5个值可选：
justify-content:flex-start; 左对齐（默认值）
{% asset_img flex-start.jpg flex-start %}

justify-content:flex-end; 右对齐
{% asset_img flex-end.jpg flex-end %}

justify-content:center; 居中
{% asset_img center.jpg centert %}

justify-content:space-between; 两端对齐，item之间的间隔都相等。
{% asset_img space-between.jpg space-between %}

justify-content:space-around; 每个item两侧的间隔相等，所以，item之间的间隔比item与边框的间隔大一倍。
{% asset_img space-around.jpg space-around %}

## 5、align-items属性定义item在侧轴上如何对齐。align-items属性有5个值可选：
align-items:flex-start; 侧轴的起点对齐。
{% asset_img align-items-flex-start.jpg align-items-flex-start %}

align-items:flex-end; 侧轴的终点对齐。
{% asset_img align-items-flex-end.jpg align-items-flex-end %}

align-items:center; 侧轴的中点对齐。
{% asset_img align-items-center.jpg align-items-center %}

align-items:baseline; item的第一行文字的基线对齐。
{% asset_img align-items-baseline.jpg align-items-baseline %}

align-items:stretch; 如果item未设置高度或设为auto，将占满整个容器的高度。
{% asset_img align-items-stretch.jpg align-items-stretch %}

## 6、align-content属性定义多行/多列对齐方式。align-content属性有6个值可选：
align-content:flex-start; 与侧轴的起点对齐。
{% asset_img align-content-flex-start.jpg align-content-flex-start %}

align-content:flex-end; 与侧轴的终点对齐。
{% asset_img align-content-flex-end.jpg align-content-flex-end %}

align-content:center; 与侧轴的中点对齐。
{% asset_img align-content-center.jpg align-content-center %}

align-content:space-between; 侧轴两端对齐，轴线之间的间隔平均分布。
{% asset_img align-content-space-between.jpg align-content-space-between %}

align-content:space-around; 每根轴线两侧的间隔都相等，所以，轴线之间的间隔比轴线与边框的间隔大一倍。
{% asset_img align-content-space-around.jpg align-content-space-around %}

align-content:stretch; 轴线占满整个侧轴（默认值）。
{% asset_img align-content-stretch.jpg align-content-stretch %}

# 三、item属性
item上有6个属性order、flex-grow、flex-shrink、flex-basis、flex、align-self。
## 1、order属性定义item的排列顺序。数值越小，排列越靠前，默认为0。
order:1;
{% asset_img order.jpg order %}

## 2、flex-grow属性定义item的放大比例，默认为0，即如果存在剩余空间，也不放大。
如果所有item的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。
如果一个item的flex-grow属性为2，其他item都为1，则前者占据的剩余空间将比其他项多一倍。
flex-grow:1; 
{% asset_img flex-grow.jpg flex-grow %}

flex-grow:2;
{% asset_img flex-grow.jpg flex-grow %}

## 3、flex-shrink属性定义了item的缩小比例，默认为1，即如果空间不足，该item将缩小。
如果所有item的flex-shrink属性都为1，当空间不足时，都将等比例缩小。
如果一个item的flex-shrink属性为0，其他item都为1，则空间不足时，前者不缩小。
flex-shrink:1;
{% asset_img flex-shrink.jpg flex-shrink %}

flex-shrink:0;
{% asset_img flex-shrink-2.jpg flex-shrink %}

## 4、flex-basis属性定义了在分配多余空间之前，item占据的主轴空间（main size）。
浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即item的本来大小。
它可以设为跟width或height属性一样的值，则item将占据固定空间。
flex-basis:230px;
{% asset_img flex-basis.jpg flex-basis %}

## 5、flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。
flex:1 1 230px;
{% asset_img flex-auto.jpg flex-auto %}

## 6、align-self属性允许单个item有与其他item不一样的对齐方式，可覆盖align-items属性。默认值为auto，
表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。
align-self:flex-end;
{% asset_img align-self.jpg align-self %}


