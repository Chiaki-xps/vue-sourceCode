## createApp

当我们第一次运行代码实现一个Hellow world的时候，vue会做些什么？

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
        // rootComponent在这里是我们的根节点
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

2. 执行render函数，内部调用了patch方法，patch函数中，会根据我们生成的虚拟节点 进行不同的处理。

```js
// 解构出type
const { type, shapeFlag } = n2;

// 根据type类型进行不同的处理
switch (type) {
 
 // shapeFlag基于二进制做的处理，通过二进制的方式获取到他的类型，以此区分不同的组件
}
```

3. 初始化组件processComponent

```ts
// n1表示之前，n2表示当前的值，用于更新比较
function processComponent(n1, n2, container, parentComponent) {
  // n1没有值的话，走初始化mountComponent
	if (!n1) {
    mountComponent(n2, container, parentComponent);
  }
  else {
    updateComponent(n1, n2);
  }
}
```

4. mountComponent

```ts
function mountComponent(initialVNode, container, parentComponent) {
  	// 1. 创建一个component instance对象
    const instance = (initialVNode.component = createComponentInstance(initialVNode, parentComponent));
    console.log(`创建组件实例:${instance.type.name}`);
  	// 2. setup
    setupComponent(instance);
    setupRenderEffect(instance, initialVNode, container);
}
```

5. setupComponent执行
   1. 初始化props
   2. 初始化slots
   3. 调用setup()，返回一个对象或者函数
   4. 设置render()函数

6. setupStatefulComponent

```ts
function setupStatefulComponent(instance) {
    console.log("创建 proxy");
  	// 1. 创建proxy
  	// 2. instance的作用就是搜集数据，为后续算法做准备	
    instance.proxy = new Proxy(instance.ctx, PublicInstanceProxyHandlers);
    const Component = instance.type;
    const { setup } = Component;
    if (setup) {
        setCurrentInstance(instance);
        const setupContext = createSetupContext(instance);
        const setupResult = setup && setup(shallowReadonly(instance.props), setupContext);
        setCurrentInstance(null);
        handleSetupResult(instance, setupResult);
    }
    else {
        finishComponentSetup(instance);
    }
}
```







## 技巧

看源码的时候尽量看自己关注的主流程，其他流程可以猜测或略过