---
title: Axios 中拦截器的实现
date: "2021-08-20"
description: "在 Axios 中拦截器是非常有用的一个功能，可以让我们实现请求前对 config 参数进行调整以及响应后的数据处理"
tags:
  - axios
---

在 Axios 中拦截器是非常有用的一个功能，可以让我们实现请求前对 config 参数进行调整以及响应后的数据处理。
由于可以添加多个请求或响应拦截器，所以很明显 Axios 的拦截器内部是一个数组，通过 use 方法进行添加拦截器，通过 eject 方法删除某个拦截器。

所以先实现拦截器管理类的第一步

- 存在一个内部变量维护拦截器的添加和删除
- 通过 use 方法添加拦截器，并返回此拦截器在数组中的位置方便删除操作
- 通过 eject 方法删除某个拦截器

```javascript
class InterceptorManager {
  constructor() {
    this.interceptors = []
  }

  use(resolved, rejected) {
    // 拦截器包含resolved和rejected两个函数
    let interceptor = {
      resolved,
      rejected,
    }
    this.interceptors.push(interceptor)
    return this.interceptors.length - 1
  }

  eject(id) {
    if (this.interceptors[id]) {
      this.interceptors[id] = null // 不改变数组长度，把对应拦截器设为null
    }
  }
}
```

通过以上拦截器的实现，那么一个简易的 Axios 类就很简单了

- 创建两个实例对应请求拦截器和响应拦截器
- 添加一个 request 方法模拟 xhr 请求，入参为 config，返回值为 response

```javascript
class Axios {
  constructor() {
    this.interceptors = {
      request: new InterceptorManager(),
      response: new InterceptorManager(),
    }
  }
  request(config) {
    return Promise.resolve(response)
  }
}
//拦截器的使用
const axios = new Axios()
axios.interceptors.request.use(config => {
  config.header.Token = "string"
  return config
})

console.log(axios.interceptors.request)
// InterceptorManager {
//   interceptors: [ { resolved: [Function (anonymous)], rejected: undefined } ]
// }
```

这样通过调用 use 方法的调用就可以把自定义的拦截器函数推入相应的拦截器数组里，但是存进数组里的方法暂时还不能够调用，那么再对 InterceptorManager 类提供一个 forEach 的方法供外部调用，具体实现为：

```javascript
class InterceptorManager {
  constructor() {
    this.interceptors = []
  }

  use(resolved, rejected) {
    // 拦截器包含resolved和rejected两个函数
    let interceptor = {
      resolved,
      rejected,
    }
    this.interceptors.push(interceptor)
    return this.interceptors.length - 1
  }

  eject(id) {
    if (this.interceptors[id]) {
      this.interceptors[id] = null // 不改变数组长度，把对应拦截器设为null
    }
  }

  forEach(fn) {
    this.interceptors.forEach(interceptor => {
      if (interceptor !== null) {
        fn(interceptor)
      }
    })
  }
}
```

剩下的工作就是在 Axios 创建实例的时候把请求拦截器加在 xhr 请求之前，把响应拦截器放在获取数据之后，而且整个步骤是按顺序链式调用的。

```javascript
class Axios {
  constructor() {
    this.interceptors = {
      request: new InterceptorManager(),
      response: new InterceptorManager(),
    }
  }
  // 模拟一个xhr请求，log此时的config，返回带有response的Promise对象
  xhr(config) {
    console.log(config)
    return Promise.resolve({ code: 200, data: "test" })
  }
  request(config) {
    //创建一个新数据组按顺序存放拦截器和请求方法
    const chain = [
      {
        resolved: this.xhr,
        rejected: undefined,
      },
    ]
    //请求拦截器放在请求调用之前
    this.interceptors.request.forEach(interceptor => {
      chain.unshift(interceptor)
    })
    //响应拦截器放在请求调用之后
    this.interceptors.response.forEach(interceptor => {
      chain.push(interceptor)
    })

    let promise = Promise.resolve(config)

    while (chain.length) {
      const { resolved, rejected } = chain.shift()
      promise = promise.then(resolved, rejected)
    }
    return promise
  }
}
```

这样整个 Axios 的拦截器功能就实现了，通过对 chain 数组的遍历完成链式调用，而且在执行到 xhr 请求时把参数从 config 换成了 response，这样响应拦截器的参数就变成了 response。

完整代码如下：

```javascript
class InterceptorManager {
  constructor() {
    this.interceptors = []
  }

  use(resolved, rejected) {
    // 拦截器包含resolved和rejected两个函数
    let interceptor = {
      resolved,
      rejected,
    }
    this.interceptors.push(interceptor)
    return this.interceptors.length - 1
  }

  eject(id) {
    if (this.interceptors[id]) {
      this.interceptors[id] = null // 不改变数组长度，把对应拦截器设为null
    }
  }

  forEach(fn) {
    this.interceptors.forEach(interceptor => {
      if (interceptor !== null) {
        fn(interceptor)
      }
    })
  }
}

class Axios {
  constructor() {
    this.interceptors = {
      request: new InterceptorManager(),
      response: new InterceptorManager(),
    }
  }
  xhr(config) {
    console.log(config)
    return Promise.resolve({ code: 200, data: "test" })
  }
  request(config) {
    //创建一个新数据组按顺序存放拦截器和请求方法
    const chain = [
      {
        resolved: this.xhr,
        rejected: undefined,
      },
    ]
    //请求拦截器放在请求调用之前
    this.interceptors.request.forEach(interceptor => {
      chain.unshift(interceptor)
    })
    //响应拦截器放在请求调用之后
    this.interceptors.response.forEach(interceptor => {
      chain.push(interceptor)
    })

    let promise = Promise.resolve(config)

    while (chain.length) {
      const { resolved, rejected } = chain.shift()
      promise = promise.then(resolved, rejected)
    }
    return promise
  }
}

const axios = new Axios()

//为config的headers属性添加Token字段
axios.interceptors.request.use(config => {
  config.headers.Token = "string"
  return config
})

//为返回值添加一个msg属性
axios.interceptors.response.use(response => {
  response.msg = "success"
  return response
})

axios
  .request({
    url: "/api",
    headers: {},
  })
  .then(res => {
    console.log(res)
  })

// config: { url: '/api', headers: { Token: 'string' } }
// reponse: { code: 200, data: 'test', msg: 'success' }
```
