# vue3.0异步更新scheduler笔记



 ## 关于instance.update()

在用effect设置，并传入更新视图的函数，和options参数，options的scheduler属性为queueJob。queueJob为执行异步更新的函数



## 关于watch的scheduler

watch会根据flush进而确定schedule的值，默认是‘pre’异步执行，会调用scheduler

去执行queuePreFlushCb()



## 关于异步queueJob的执行



```typescript
export function queueJob(job: SchedulerJob) {
 if (
  (!queue.length ||
   !queue.includes(
   job,
   isFlushing && job.allowRecurse ? flushIndex + 1 : flushIndex
   )) &&
  job !== currentPreFlushParentJob
 ) {
  queue.push(job)
  queueFlush()
 }
}
```

首先判断是否queue为空，若不为空再判断当前若在执行isFlushing状态下是否在flushIndex后存在。          

之后将job回调函数加入queue数组中，并且执行queueFlush。



```javascript
function queueFlush() {
  if (!isFlushing && !isFlushPending) {
     // 保证只有一个flushJobs
    isFlushPending = true
    currentFlushPromise = resolvedPromise.then(flushJobs)
  }
}
```

此函数是将flushJobs执行异步的函数，加入promise中。在这之前，判断isFlushing和isFlushPending是否运行中。iSFlushPending是为了保证promise中只加入一次flushJobs，不然加入多个flushjiobs会执行多次回调。       





```typescript
function flushJobs(seen?: CountMap) {
  isFlushPending = false
  isFlushing = true
  if (__DEV__) {
    seen = seen || new Map()
  }

  // 执行flsh=‘pre’时的回调 ，组件更新前执行
  flushPreFlushCbs(seen)

  // 进行排序
  queue.sort((a, b) => getId(a!) - getId(b!))

  // 执行回调
  try {
    for (flushIndex = 0; flushIndex < queue.length; flushIndex++) {
      const job = queue[flushIndex]
      if (job) {
        if (__DEV__) {
          // 不允许嵌套调用
          checkRecursiveUpdates(seen!, job)
        }
        callWithErrorHandling(job, null, ErrorCodes.SCHEDULER)
      }
    }
  } finally {
    // 恢复
    flushIndex = 0
    queue.length = 0

    flushPostFlushCbs(seen)

    isFlushing = false
    currentFlushPromise = null
    // some postFlushCb queued jobs!
    // keep flushing until it drains.
    if (queue.length || pendingPostFlushCbs.length) {
      flushJobs(seen)
    }
  }
}
```



以上当执行flushJobs时，改变isFlushPending和isFlushing的状态并且先执行pre的回调函数，再排序再执行。

排序是为了1.组件从父组件到子组件。2.如果父组件更新期间删除了子组件，可以跳过更新。

 

1. 从父组件更新到子组件。(因为父母总是在子元素之前创建，所以渲染效果会更小优先级编号)

2. 如果在父组件更新期间卸载了组件，可以跳过它的更新。



关于flushPreFlushCbs(seen)的执行，先要回看到watchEffect和watch的scheduler。



```typescript
// activePreFlushCbs用来存放正在执行的回调函数，pendingPreFlushCbs用来存储还未执行的回调函数
export function queuePreFlushCb(cb: SchedulerCb) {
  queueCb(cb, activePreFlushCbs, pendingPreFlushCbs, preFlushIndex)
}
```



```typescript
function queueCb(
  cb: SchedulerCbs,
  activeQueue: SchedulerCb[] | null,
  pendingQueue: SchedulerCb[],
  index: number
) {
  if (!isArray(cb)) {
    // 判断 activeQueue是否正在执行回掉函数，若正在执行判断回调是否已存在
    if (
      !activeQueue ||
      !activeQueue.includes(
        cb,
        (cb as SchedulerJob).allowRecurse ? index + 1 : index
      )
    ) {
      // 成功添加函数到pendingQueue中
      pendingQueue.push(cb)
    }
  } else {
    pendingQueue.push(...cb)
  }
  // 放入promise
  queueFlush()
}
```



之后当执行flushJobs时就会执行到flushPreFlushCbs

```typescript
export function flushPreFlushCbs(
  seen?: CountMap,
  parentJob: SchedulerJob | null = null
) {
  //  判断等待pre回调是否为空数组
  if (pendingPreFlushCbs.length) {
    currentPreFlushParentJob = parentJob
    // 去重
    activePreFlushCbs = [...new Set(pendingPreFlushCbs)]
    // 准备执行activePreFlushCbS的回调，设置pendingPreFlushCbs为空数组，方便新的回调加入
    pendingPreFlushCbs.length = 0
    if (__DEV__) {
      seen = seen || new Map()
    }
    // for循环遍历
    for (
      preFlushIndex = 0;
      preFlushIndex < activePreFlushCbs.length;
      preFlushIndex++
    ) {
      // 检查是否嵌套调用
      if (__DEV__) {
        // 循环嵌套100次报错
        checkRecursiveUpdates(seen!, activePreFlushCbs[preFlushIndex])
      }
      activePreFlushCbs[preFlushIndex]()
    }
    // 复原
    activePreFlushCbs = null
    preFlushIndex = 0
    currentPreFlushParentJob = null
    // 执行本轮产生的新的回调函数
    flushPreFlushCbs(seen, parentJob)
  }
}
```



```typescript

function checkRecursiveUpdates(seen: CountMap, fn: SchedulerJob | SchedulerCb) {
   // 若不存在添加
  if (!seen.has(fn)) {
    seen.set(fn, 1)
  } else {
    const count = seen.get(fn)!
    // 存在超过100次报错
    if (count > RECURSION_LIMIT) {
      throw new Error(
        `Maximum recursive updates exceeded. ` +
          `This means you have a reactive effect that is mutating its own ` +
          `dependencies and thus recursively triggering itself. Possible sources ` +
          `include component template, render function, updated hook or ` +
          `watcher source function.`
      )
    } else {
      seen.set(fn, count + 1)
    }
  }
}

```





