# vue3.0源码ref学习

vue中的ref主要使用在想使用单个变量响应式。    

toRefs 可以将reactive转换为单个响应式，setup中return出去后可以在模板访问。



```typescript
export function ref<T extends Ref>(raw: T): T
export function ref<T>(raw: T): Ref<T>
export function ref<T = any>(): Ref<T>
export function ref(raw?: unknown) {
  // 已经是ref对象了，直接返回原始值
  if (isRef(raw)) {
    return raw
  }
  // 转化为ref对象
  raw = convert(raw)
  const r = {
    _isRef: true,
    get value() {
      // getter触发时，触发依赖收集，源码在effect部分，在笔者下一篇文章会有讲述
      track(r, OperationTypes.GET, 'value')
      return raw
    },
    set value(newVal) {
      // setter触发时，首先调用convert转化为ref对象
      raw = convert(newVal)
      // trigger通知deps，通知依赖这一状态的对象更新
      trigger(
        r,
        OperationTypes.SET,
        'value',
        __DEV__ ? { newValue: newVal } : void 0
      )
    }
  }
  return r
}

// convert的作用是创建响应式包装对象，这里直接使用reactive，其原理在上一节有讲过
const convert = <T extends unknown>(val: T): T =>
  isObject(val) ? reactive(val) : val



```



返回一个对象v，可以直接在模板使用



```typescript
export function toRefs<T extends object>(
  object: T
): { [K in keyof T]: Ref<T[K]> } {
  const ret: any = {}
  // 遍历对象的所有属性，都对其调用toProxyRef
  for (const key in object) {
    ret[key] = toProxyRef(object, key)
  }
  return ret
}

// toProxyRef相当于把对象的每个属性都变成一个包装对象，这样在结构和使用拓展运算符时，就不会丢失原有响应式对象的引用了
function toProxyRef<T extends object, K extends keyof T>(
  object: T,
  key: K
): Ref<T[K]> {
  return {
    _isRef: true,
    get value(): any {
      return object[key]
    },
    set value(newVal) {
      object[key] = newVal
    }
  } as any
}

```

torefs可以将reactive转换为普通对象，其中结果对象上的每个属性都是指向原始对象中相应属性的`ref`引用对象。