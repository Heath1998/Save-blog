# Vue3.0的源码effect笔记



## 前言

首先在vue3.0出现后，抛弃了vue2.0的Watcher渲染的概念，而是使用了effect副作用函数，来完成依赖，完成视图及其他改变。



## effect源码分析



```typescript
export function effect<T = any>(
  fn: () => T,
  options: ReactiveEffectOptions = EMPTY_OBJ
): ReactiveEffect<T> {
  if (isEffect(fn)) {
    fn = fn.raw
  }
  //主要逻辑就是返回createReactiveEffect(),options是参数
  const effect = createReactiveEffect(fn, options)
  // 没有惰值，马上执行
  if (!options.lazy) {
    effect()
  }
  return effect
}
```



```typescript
function createReactiveEffect<T = any>(
  fn: () => T,
  options: ReactiveEffectOptions
): ReactiveEffect<T> {
      // 设置需要返回的effect函数
  const effect = function reactiveEffect(): unknown {
    if (!effect.active) {
      return options.scheduler ? undefined : fn()
    }
    // 使用effectStack避免循环嵌套，如
	//const nums = reactive({ num1: 0, num2: 1 })
	//
 	// const spy1 = jest.fn(() => (nums.num1 = nums.num2+1))
  	// const spy2 = jest.fn(() => (nums.num2 = nums.num1+1))
    //effect(spy1)
    //effect(spy2)
    //这样effStack可以避免循环调用
    if (!effectStack.includes(effect)) {
      cleanup(effect)
      try {
        enableTracking()
        effectStack.push(effect)
        activeEffect = effect
        return fn()
      } finally {
        effectStack.pop()
        resetTracking()
        activeEffect = effectStack[effectStack.length - 1]
      }
    }
  } as ReactiveEffect
  // 添加属性
  effect.id = uid++
  effect.allowRecurse = !!options.allowRecurse
  effect._isEffect = true
  effect.active = true
  effect.raw = fn
  effect.deps = []
  effect.options = options
  return effect
}
```



总的来说effect主要使用来作为订阅器，当监听的数据发生改变，再执行。



