## reactive

1. reactive接受一个参数

```ts
export function reactive(target) {
  return createReactiveObject(target, reactiveMap, mutableHandlers);
}
```

2. 通过createReactiveObject去创建一个Proxy

```ts
function createReactiveObject(target, proxyMap, baseHandlers) {
  // 核心就是 proxy
  // 目的是可以侦听到用户 get 或者 set 的动作

  // 如果命中的话就直接返回就好了
  // 使用缓存做的优化点
  const existingProxy = proxyMap.get(target);
  if (existingProxy) {
    return existingProxy;
  }
	
  // baseHandlers 处理器
  const proxy = new Proxy(target, baseHandlers);

  // 把创建好的 proxy 给存起来，
  proxyMap.set(target, proxy);
  return proxy;
}

```

