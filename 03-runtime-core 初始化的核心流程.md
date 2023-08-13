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
        const vnode = createVNode(rootComponent);
        console.log("调用 render，基于 vnode 进行开箱");
        render(vnode, rootContainer);
      },
    };

    return app;
  };
}

```











## 技巧

看源码的时候尽量看自己关注的主流程，其他流程可以猜测或略过