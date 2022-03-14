---
title: 发布订阅
date: "2022-03-14"
description: "实现一个简易的发布订阅。"
tags:
  - 发布订阅
  - 设计模式
---

### 发布订阅

- 创建一个 EventListener
- 给 EventListener 添加一个缓存列表，用于存放回调函数以便通知订阅者
- 发布消息时，EventListener 遍历缓存列表，依次触发回调
- 故 EventListener 应该有 emit (发布) 、on (订阅) 、off (取消订阅) 等方法。


```javascript
class EventListener {
  listeners = [];

  on(name, fn) {
    let listener = {
      name,
      fn,
      isOnce: false,
    };
    this.listeners.push(listener);
    return listener;
  }

  once(name, fn) {
    let listener = {
      name,
      fn,
       isOnce: true,
    };
    this.listeners.push(listener);
    return listener;
  }

  off(listener) {
    let index = this.listeners.indexOf(listener);
    if (index !== -1) {
      this.listeners.splice(index, 1);
    }
  }

  emit(name, args) {
    this.listeners.forEach((listener) => {
      try {
        if (listener.name === name) {
          if (listener.isOnce) {
            this.off(listener);
          }
          listener.fn(args);
        }
      } catch (e) {
        console.error(e);
      }
    });
  }
}

```

