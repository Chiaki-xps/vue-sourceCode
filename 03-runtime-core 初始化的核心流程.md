## createApp

1. createApp函数中，会先创建一个app对象

```js
import { createVNode } from "./vnode";

// createApp(App).mount("#root");
export function createAppAPI(render) {
  
  return function createApp(rootComponent) {
    const app = {
      _component: rootComponent,
      mount(rootContainer) {
          
        console.log("基于根组件创建 vnode");
        // 1. 根据传入的rootComponent创建虚拟节点
        // 生成的vnode节点中有一个对象type，里面包含了传入的App的信息
        const vnode = createVNode(rootComponent);
          
        console.log("调用 render，基于 vnode 进行开箱");
        // 2. 进行render,render函数调用了patch方法
        render(vnode, rootContainer);
      },
    };

    // 3. 导出的app就会有关键的mount方法
    return app;
  };
}

```

2. 执行render函数，内部调用了patch，patch函数中，会根据我们生成的虚拟节点 进行不同的处理。

```js
// 解构出type
const { type, shapeFlag } = n2;

// 根据type类型进行不同的处理
switch (type) {
 
 // shapeFlag基于二进制做的处理，通过二进制的方式获取到他的类型，以此区分不同的组件
}
```

3. 初始化组件processComponent











## 技巧

看源码的时候尽量看自己关注的主流程，其他流程可以猜测或略过