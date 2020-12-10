##  Vue的mixin源码学习



### Vue.mixin全局混入

```javascript
export function initMixin (Vue: GlobalAPI) {
  Vue.mixin = function (mixin: Object) {
    this.options = mergeOptions(this.options, mixin)
    return this
  }
}
```



全局混入也是一个执行mergeOptions的过程，来看看与mixins相关的代码

```javascript
// vue/src/core/util/options.js
export function mergeOptions (
  parent: Object,
  child: Object,
  vm?: Component
): Object {

    ........
    
if (child.mixins) { // 判断有没有mixin 也就是mixin里面挂mixin的情况 有的话递归进行合并
    for (let i = 0, l = child.mixins.length; i < l; i++) {
    parent = mergeOptions(parent, child.mixins[i], vm)
    }
}

  const options = {} 
  let key
  for (key in parent) {
    mergeField(key) // 先遍历parent的key 调对应的strats[XXX]方法进行合并
  }
  for (key in child) {
    if (!hasOwn(parent, key)) { // 如果parent已经处理过某个key 就不处理了
      mergeField(key) // 处理child中的key 也就parent中没有处理过的key
    }
  }
  function mergeField (key) {
    const strat = strats[key] || defaultStrat
    options[key] = strat(parent[key], child[key], vm, key) // 根据不同类型的options调用strats中不同的方法进行合并
  }
  return options
}

```

由上可知主要是合并，来看一下strats合并方法有哪些。



如果是全局混合就会直接混合，结果大概为



```javascript
const mixin = {
  data() {
    return {
      test: 1234567
    }
  }
} 
Vue.mixin(mixin)

// Vue构造函数结果的options为
options: {
    components: {}
    data: ƒ data()
    directives: {}
    filters: {}
    _base: ƒ Vue(options)
    __proto__: Object
}
```



### 选项合并

```javascript
vm.$options = mergeOptions(
    // 这里是Vue构造函数的options属性
    resolveConstructorOptions(vm.constructor), 
    // 当前传入的options属性
    options || {},
    vm
```



```javascript
if (child.mixins) { // 判断有没有mixin 也就是mixin里面挂mixin的情况 有的话递归进行合并
    for (let i = 0, l = child.mixins.length; i < l; i++) {
    parent = mergeOptions(parent, child.mixins[i], vm)
    }
}
```

在这里就会进行一次合并，遍历mixins属性下的每一个mixin



### 几种合并方式

#### data合并型

```javascript
strats.data = function(parentVal, childVal, vm) {    
    return mergeDataOrFn(
        parentVal, childVal, vm
    )
};

function mergeDataOrFn(parentVal, childVal, vm) {    
    return function mergedInstanceDataFn() {        
        var childData = childVal.call(vm, vm) // 执行data挂的函数得到对象
        var parentData = parentVal.call(vm, vm)        
        if (childData) {            
            return mergeData(childData, parentData) // 将2个对象进行合并                                 
        } else {            
            return parentData // 如果没有childData 直接返回parentData
        }
    }
}

function mergeData(to, from) {    
    if (!from) return to    
    var key, toVal, fromVal;    
    var keys = Object.keys(from);   
    for (var i = 0; i < keys.length; i++) {
        key = keys[i];
        toVal = to[key];
        fromVal = from[key];    
        // 如果不存在这个属性，就重新设置
        if (!to.hasOwnProperty(key)) {
            set(to, key, fromVal);
        }      
        // 存在相同属性，合并对象
        else if (typeof toVal =="object" && typeof fromVal =="object") {
            mergeData(toVal, fromVal);
        }
    }    
    return to
}

```



可以看到data主要就是返回一个函数，当`initData`时需要获取data时会执行这个函数执行合并获取data对象。



这里有个注意的为什么合并需要要求data为函数，即组件中的数据为什么都只能用data函数返回。



```javascript
// 策略合并 data 
strats.data = function (
 parentVal: any,
 childVal: any,
 vm?: Component // 只有是 vm = Vue 根实例的时候， 第三个参数才会调用到
): ?Function {
 // 第一次响应的时候 是 谁触发的 ？  根实例吗，no 
 // 如果是一个普通的实例
 if (!vm) {
   // run  --> vm = Vue 的时候才会走
   if (childVal && typeof childVal !== 'function') {
     process.env.NODE_ENV !== 'production' && warn(
       'The "data" option should be a function ' +
       'that returns a per-instance value in component ' +
       'definitions.',
       vm
     )

     return parentVal
   }
   // 并没有传递 vm
   return mergeDataOrFn(parentVal, childVal)
 }
 // 如果是根实例会走下面的逻辑
 return mergeDataOrFn(parentVal, childVal, vm)
}
```

这里的vm只有不为组件才存在，即new Vue。为什么不允许使用data对象作为合并呢？      

因为当你有多个组件时，会有多个子组件在mergeOptions的合并data中，共用这个data对象。用函数的话可以返回全新对象。



#### 替换型props、methods、inject、computed

```javascript
strats.props =
strats.methods =
strats.inject =
strats.computed = function (
  parentVal: ?Object,
  childVal: ?Object,
  vm?: Component,
  key: string
): ?Object {
  if (!parentVal) return childVal // 如果parentVal没有值，直接返回childVal
  const ret = Object.create(null) // 创建一个第三方对象 ret
  extend(ret, parentVal) // extend方法实际是把parentVal的属性复制到ret中
  if (childVal) extend(ret, childVal) // 把childVal的属性复制到ret中
  return ret
}
strats.provide = mergeDataOrFn

```

大体意思将父子合并，如果父子某个属性都有，就用子属性替换父属性。



#### 叠加型component、directives、filters

```javascript
strats.components=
strats.directives=

strats.filters = function mergeAssets(
    parentVal, childVal, vm, key
) {    
    var res = Object.create(parentVal || null);    
    if (childVal) { 
        for (var key in childVal) {
            res[key] = childVal[key];
        }   
    } 
    return res
}
```

用create创建一个将父级作为原型链的对象res，再将子元素属性复制到res对象上。



#### 队列型全部生命周期函数和watch

```javascript
function mergeHook (
  parentVal: ?Array<Function>,
  childVal: ?Function | ?Array<Function>
): ?Array<Function> {
  return childVal
    ? parentVal
      ? parentVal.concat(childVal)
      : Array.isArray(childVal)
        ? childVal
        : [childVal]
    : parentVal
}

LIFECYCLE_HOOKS.forEach(hook => {
  strats[hook] = mergeHook
})

```

Vue 实例的生命周期钩子被合并为一个数组，然后正序遍历一次执行。



