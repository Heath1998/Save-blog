# vue3.0响应式reactive原理理解



vue3.0增加了composition api的形式，删除了原来的watcher和dep的依赖收集形式。转而采取targetMap的WeakMap的形式保存。

具体是以下图的形式确立



![11 (1)](C:\Users\80302152\Downloads\11 (1).png)



targetMap是一个`new weakMap()`它的键值是一个对象但不为null，value值是一个哈希表，包含这个对象中所有属性所对应的依赖收集Set数组，是一个哈希表。      

depMap是一个哈希表Map，key值为当前对象的所有属性，value值是一个Set数组。        

dep是一个Set数组包含所有已注册的依赖。





可以理解vue3.0运用到了proxy，从而放弃了IE。

```js
new Proxy(target, handler)
```

* `target`： 所要拦截的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）
* `handler`：一个对象，定义要拦截的行为



在执行`new Proxy(target, handler)`时，放入baseHandler进行拦截。



```javascript
const baseHandler = {
 get(target, key) {
   // Reflect.get
   const res = Reflect.get(target, key);
   // @todo 依赖收集
   // 尝试获取值obj.age，触发getter
   track(target, key);
   return typeof res === "object" ? reactive(res) : res;
 },
 set(target, key, val) {
   const info = { oldValue: target[key], newValue: val };
   // Reflect.set
   // target[key] = val;
   const res = Reflect.set(target, key, val);
   // @todo 响应式去通知变化 触发执行，effect函数是响应式对象修改触发的
   trigger(target, key, info);
 },
};
```

Reflect作用：优化Object的一些操作方法以及合理的返回Object操作返回的结果。   



基本逻辑如上图，当访问数据触发get方法，判断是否为`object`是的话会深度遍历`new Proxy`,还有track进行依赖收集。        

如果数据改变会触发set，trigger进行派发。



那具体track和trigger做了什么？           

简单来讲主要逻辑track就是获取depMap即·`targetMap.get(target)`，不存在就自己创建一个，再通过获取的depMap获取key值对应的Set依赖数组，没有还是自己创建，最后将effectActive加入dep依赖数组中。



trigger主要逻辑讲了

通过key找到depMap[key]即找到对应的dep，并且遍历派发。





```typescript
  const run = (effect: ReactiveEffect) => {
    if (__DEV__ && effect.options.onTrigger) {
      effect.options.onTrigger({
        effect,
        target,
        key,
        type,
        newValue,
        oldValue,
        oldTarget
      })
    }
    if (effect.options.scheduler) {
      effect.options.scheduler(effect)
    } else {
      effect()
    }
  }

  effects.forEach(run)
```

