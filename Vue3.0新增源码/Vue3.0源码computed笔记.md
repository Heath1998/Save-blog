# Vue3.0源码computed笔记



```typescript
export function computed<T>(
  options: WritableComputedOptions<T>
): WritableComputedRef<T>
export function computed<T>(
  getterOrOptions: ComputedGetter<T> | WritableComputedOptions<T>
) {
  let getter: ComputedGetter<T>
  let setter: ComputedSetter<T>
  if (isFunction(getterOrOptions)) {  /* 处理只有get函数的逻辑 */
    getter = getterOrOptions
    setter = () => {}
  } else { /* 还有 getter 和 setter情况 */
    getter = getterOrOptions.get
    setter = getterOrOptions.set
  }
  let dirty = true
  let value: T
  let computed: ComputedRef<T>
  const runner = effect(getter, {
    lazy: true,
    computed: true,
    scheduler: () => {
      if (!dirty) {
        dirty = true /* 派发所有引用当前计算属性的副作用函数effect */
        trigger(computed, TriggerOpTypes.SET, 'value')
      }
    }
  })
  computed = {
    _isRef: true,
    effect: runner,
    get value() { 
      if (dirty) {
        /* 运行computer函数内容 */
        value = runner()
        dirty = false
      }/* 收集引入当前computer属性的依赖 */
      track(computed, TrackOpTypes.GET, 'value')
      return value
    },
    set value(newValue: T) {
      setter(newValue)
    }
  } as any
  return computed
}

```



上面是computed的源代码。获取computed传入的get和set并赋值。    

设置脏值，初始值为true，当访问computed的value值时才会执行runner去获取值，之后设置脏值为false，这样继续访问computed

的value值时就会返回之前的value值，只有当依赖的数据改变时才会设置脏值为true，这样当访问computed的value时才会重新去执行runner()获取新值，并返回。