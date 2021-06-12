---
title: 手写系列之二：EventHub
date: 2021-06-11 17:20:12
tags: ["EventHub", "手写系列"]
categories: JavaScript
---

# 何时使用EventHub

曾经在实际开发工作中遇到如下场景：在一个页面中有2个Tab页(Tab之间切换不会刷新页面)，其中Tab A展示接入设备的数量使用情况；Tab B可允许用户对设备接入数量进行扩容，要求在Tab B成功进行扩容后Tab A随后自动更新数据。EventHub则正好适用于这种场景。EventHub可用于模块之间通信：Tab A注册/监听(on)更新事件，在Tab B触发(emit)更新事件后，Tab A做出相应行为。

# 实现EventHub

EventHub包含3个方法：on(注册/监听)、emit(触发)、off(取消)。

<!-- more -->

- on 方法

  ````typescript
  class EventHub {
      cache: {[key: string]: Array<(data: unknown) => void>} = {}
      //cache的类型是对象，key的类型是string，value的类型是数组，数组中的函数接受一个参数，参数类型unknown，返回值void。
      
      //cache的结构
      //{
      // 'eventName1': [fn1, fn2, fn3],
      // 'eventName2': [fn4, fn5, fn6],
      //}
      
      //把fn存入this.cache[eventName]数组
      on (eventName: string, fn: (data: unknown) => void) { 
      //接收2个参数，第一个参数是注册/监听的事件名(类型为string)，第二个参数是事件触发后做出的行为函数(接受一个参数，参数类型unknown，返回值void)。
          this.cache[eventName] = this.cache[eventName] || [];//初始化
          this.cache[eventName].push(fn);
      }
  }
  ````

  

- emit 方法

  ````typescript
  class EventHub {
      cache: {[key: string]: Array<(data: unknown) => void>} = {}
      //cache的类型是对象，key的类型是string，value的类型是数组，数组中的函数接受一个参数，参数类型unknown，返回值void。
      
      //把this.cache[eventName]数组中的fn依次调用
      emit (eventName: string, data: unknown) { 
      //接收2个参数，第一个参数是触发的事件名(类型为string)，第二个参数是触发事件传递的数据(参数类型unknown)。
          (this.cache[eventName] || []).forEach(fn => fn(data));
      }
  }
  ````

  

- off 方法

````typescript
class EventHub {
    cache: {[key: string]: Array<(data: unknown) => void>} = {}
    //cache的类型是对象，key的类型是string，value的类型是数组，数组中的函数接受一个参数，参数类型unknown，返回值void。
    
    //把this.cache[eventName]数组中的fn删除
    off (eventName: string, fn: (data: unknown) => void) { 
    //接收2个参数，第一个参数是注册/监听的事件名(类型为string)，第二个参数是事件触发后做出的行为函数(接受一个参数，参数类型unknown，返回值void)。
      let index = indexOf(this.cache[eventName], fn);
      if (index === -1) return;
      this.cache[eventName].splice(index, 1);
    }
}

function  indexOf(array, fn) {//找到fn在this.cache[eventName]中的位置
  let index = -1;
  if (array === undefined) return -1;

  for(let i = 0; i < array.length; i++) {
    if (array[i] === fn) {
      index = i;
      break;
    }
  }
  return index;
}
````

至此，on、emit、off均已实现，[完整代码](https://github.com/wuwenxing0912/hand-writing/tree/master/EventHub)如下：

````typescript
 class EventHub {
   cache: {[key: string]: Array<(data: unknown) => void>} = {}

   on(eventName: string, fn: (data: unknown) => void) {
     this.cache[eventName] = this.cache[eventName] || [];
     this.cache[eventName].push(fn);
   }

   emit(eventName: string, data?: unknown) {
     (this.cache[eventName] || []).forEach(fn => fn(data));
   }

   off(eventName: string, fn: (data: unknown) => void) {
     let index = indexOf(this.cache[eventName], fn);
     if (index === -1) return;
     this.cache[eventName].splice(index, 1);
   }
 };

function  indexOf(array, fn) {
  let index = -1;
  if (array === undefined) return -1;

  for(let i = 0; i < array.length; i++) {
    if (array[i] === fn) {
      index = i;
      break;
    }
  }
  return index;
}
````

