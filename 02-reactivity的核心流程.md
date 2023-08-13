# reactive

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

## get

1. 调用createGetter()

```ts
const get = createGetter(); // 返回的是一个get函数

function createGetter() {
  ......
  // res就是获取属性key的值value
  const res = Reflect.get(target, key, receiver);
  ......
}
```

## set

```ts
const set = createSetter();

function createSetter() {
  return function set(target, key, value, receiver) {
    // 对key重新设置值
    const result = Reflect.set(target, key, value, receiver);

    // 在触发 set 的时候进行触发依赖
    trigger(target, 'set', key);

    return result;
  };
}
```

## effect

用于依赖收集，触发依赖

### 收集依赖

例子:

```ts
let dummy
const counter = reactive({num: 0})
effect(() => (dummy = counter.num))
```

1. effect(fn)。接受用户传入的函数
2. 通过ReactiveEffect创建_effect对象

```ts
const _effect = new ReactiveEffect(fn);
```

3. 执行fn

```ts
_effect.run();
```

4. 触发函数的时候就会触发reactive对象中的get操作
5. 触发get，执行get函数（因为我们的get属性是由createGetter方法方法创建的，放回的是一个get函数。）。返回的get函数被执行，触发track 

6. track函数执行，根据传入的type找到对应的dep（容器）

## 触发依赖

当发生set操作的时候，就会触发返回的set函数

```ts
counter.num = 7;
```

1. 执行set函数，触发trigger函数

```ts
function createSetter() {
  return function set(target, key, value, receiver) {
    // 对key重新设置值
    const result = Reflect.set(target, key, value, receiver);

    // 在触发 set 的时候进行触发依赖
    trigger(target, 'set', key);

    return result;
  };
}
```

2. 找到deps，遍历所有effects并执行
3. 执行的时候，会重新触发get操作，重新执行收集依赖过程

### 总结

reactive对象中会有一个depsMap（容器），里面收集和这个reactive相关的effects

 

















