# vue3.0源码watch和watchEffect笔记



其实vue3后vue已经取消了watch渲染，而是采取effect来完成，更改视图。



```javascript
 /* 创建一个渲染 effect */
instance.update = effect(function componentEffect() {
    //...省去的内容后面会讲到
},{ scheduler: queueJob })

```



## watch和watchEffect

```typescript
export function watch<T = any>(
  source: WatchSource<T> | WatchSource<T>[],  /* getter方法  */
  cb: WatchCallback<T>,                       /* hander回调函数 */
  options?: WatchOptions                      /* watchOptions */
): StopHandle { 
  return doWatch(source, cb, options)
}

```



```typescript
export function watchEffect(
  effect: WatchEffect,
  options?: BaseWatchOptions
): StopHandle {
  return doWatch(effect, null, options)
}

```



从两者的源码可以知道，主要的逻辑都在doWatch这个函数上实现。watch和watchEffect的区别就是watch是主动侦听目标属性，属性发生变化，执行回调。而watchEffect是一开始直接执行一次回掉函数，再监听回调函数中引用到的响应式数据，当响应式数据变化，才执行回调。    

doWatch的第二个参数用来判断是watch还是watchEffect。



## doWatch代码分析

```typescript
function doWatch(
  source: WatchSource | WatchSource[] | WatchEffect,
  cb: WatchCallback | null,
  { immediate, deep, flush, onTrack, onTrigger }: WatchOptions = EMPTY_OBJ
): StopHandle {
  /* 此时的 instance 是当前正在初始化操作的 instance  */
  const instance = currentInstance
  let getter: () => any
  if (isArray(source)) { /*  判断source 为数组 ，此时是watch情况 */
    getter = () =>
      source.map(
        s =>
          isRef(s)
            ? s.value
            : callWithErrorHandling(s, instance, ErrorCodes.WATCH_GETTER)
      )
  /* 判断ref情况 ，此时watch api情况*/
  } else if (isRef(source)) {
    getter = () => source.value
   /* 正常watch情况，处理getter () => state.count */
  } else if (cb) { 
    getter = () =>
      callWithErrorHandling(source, instance, ErrorCodes.WATCH_GETTER)
  } else {
    /*  watchEffect 情况 */
    getter = () => {
      if (instance && instance.isUnmounted) {
        return
      }
      if (cleanup) {
        cleanup()
      }
      return callWithErrorHandling(
        source,
        instance,
        ErrorCodes.WATCH_CALLBACK,
        [onInvalidate]
      )
    }
  }
   /* 处理深度监听逻辑 */
  if (cb && deep) {
    const baseGetter = getter
    /* 将当前 */
    getter = () => traverse(baseGetter())
  }

  let cleanup: () => void
  /* 清除当前watchEffect */
  const onInvalidate: InvalidateCbRegistrator = (fn: () => void) => {
    cleanup = runner.options.onStop = () => {
      callWithErrorHandling(fn, instance, ErrorCodes.WATCH_CLEANUP)
    }
  }
  
  let oldValue = isArray(source) ? [] : INITIAL_WATCHER_VALUE

  const applyCb = cb
    ? () => {
        if (instance && instance.isUnmounted) {
          return
        }
        const newValue = runner()
        if (deep || hasChanged(newValue, oldValue)) {
          if (cleanup) {
            cleanup()
          }
          callWithAsyncErrorHandling(cb, instance, ErrorCodes.WATCH_CALLBACK, [
            newValue,
            oldValue === INITIAL_WATCHER_VALUE ? undefined : oldValue,
            onInvalidate
          ])
          oldValue = newValue
        }
      }
    : void 0
  /* TODO:  scheduler事件调度*/
  let scheduler: (job: () => any) => void
  if (flush === 'sync') { /* 同步执行 */
    scheduler = invoke
  } else if (flush === 'pre') { /* 在组件更新之前执行 */
    scheduler = job => {
      if (!instance || instance.isMounted) {
        queueJob(job)
      } else {
        job()
      }
    }
  } else {  /* 正常情况 */
    scheduler = job => queuePostRenderEffect(job, instance && instance.suspense)
  }
  const runner = effect(getter, {
    lazy: true, /* 此时 lazy 为true ,当前watchEffect不会立即执行 */
    computed: true,
    onTrack,
    onTrigger,
    scheduler: applyCb ? () => scheduler(applyCb) : scheduler
  })

  recordInstanceBoundEffect(runner)
  /* 执行watcherEffect函数 */
  if (applyCb) {
    if (immediate) {
      applyCb()
    } else {
      oldValue = runner()
    }
  } else {
    runner()
  }
  /* 返回函数 ，用终止当前的watchEffect */
  return () => {
    stop(runner)
    if (instance) {
      remove(instance.effects!, runner)
    }
  }
}

```



## 对于watch主要有以下几部

### 1生成getter

根据传入的source的类型不同进而进行不同的处理。如果是单一属性，则设置deep为true，并且设置getter为箭头函数。



### 2判断cb和deep进行深度监测

如果有cb和deep就产生新的函数getter，通过返回traverse(baseGetter())当执行这条时，会进行依赖收集。将当前属性下的所有值都进行一次遍历，收集。



### 3设置scheduler

如果是watch会设置scheduler为cb回调函数



### 4创建runner

创建runner，并配置参数



### 5执行runner()

如果是watch的话，执行会去执行getter()进行依赖收集。



## 对于watchEffect的过程

### 设置scheduler

若runner.active为false，则直接return。不做任何操作，这时代表监听已经停止。



### 创建runner

创建runner后会马上执行



### 返回值是一个函数

返回一个函数stop，执行stop就停止监听





