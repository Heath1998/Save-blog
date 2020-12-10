## Vue的provide和inject源码学习



provide和inject本身就是用来传递信息的，不过传递的数据不是响应式，不过如果传递的是响应式数据如一个对象就会，响应式更改。



### provide和inject基础使用



```javascript
  provide() {
    return {
      reload: this.count
    }
  },
  
  // inject
  inject: {
    reload: {
      default:''
    },
    no:{
      default:'exist'
    }
  },
  
  // 如果no不存在会报错
  ineject :['reload'. 'no']
```



### provide和inject执行源码

```javascript
Vue.prototype._init = function(options?: Object) {
	
	//....
	initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm) // resolve injections before data/props
    initState(vm)
    initProvide(vm) // resolve provide after data/props
    callHook(vm, 'created')
	//....
}
```



#### initProvide(vm)

```javascript
export function initProvide (vm: Component) {
  const provide = vm.$options.provide
  if (provide) {
    vm._provided = typeof provide === 'function'
      ? provide.call(vm)
      : provide
  }
}
```

这里主要是通过判断当前provide属性是否存在，若存在执行函数函数在vm执行环境下，赋值给vm._provided



####  initInjections(vm)

```javascript
export function initInjections (vm: Component) {
  const result = resolveInject(vm.$options.inject, vm)
  if (result) {
    toggleObserving(false)
    Object.keys(result).forEach(key => {
      /* istanbul ignore else */
      if (process.env.NODE_ENV !== 'production') {
        defineReactive(vm, key, result[key], () => {
          warn(
            `Avoid mutating an injected value directly since the changes will be ` +
            `overwritten whenever the provided component re-renders. ` +
            `injection being mutated: "${key}"`,
            vm
          )
        })
      } else {
        defineReactive(vm, key, result[key])
      }
    })
    toggleObserving(true)
  }
}

export function resolveInject (inject: any, vm: Component): ?Object {
  if (inject) {
    // inject is :any because flow is not smart enough to figure out cached
    const result = Object.create(null)
    const keys = hasSymbol
      ? Reflect.ownKeys(inject).filter(key => {
        /* istanbul ignore next */
        return Object.getOwnPropertyDescriptor(inject, key).enumerable
      })
      : Object.keys(inject)

	// 通过source.$parent向上不停的查找，知道找到_provided上的自己的属性key，再赋值。
    for (let i = 0; i < keys.length; i++) {
      const key = keys[i]
      const provideKey = inject[key].from
      let source = vm
      while (source) {
        if (source._provided && hasOwn(source._provided, provideKey)) {
          result[key] = source._provided[provideKey]
          break
        }
        source = source.$parent
      }
      // 如果找不到就用default的默认值
      if (!source) {
        if ('default' in inject[key]) {
          const provideDefault = inject[key].default
          result[key] = typeof provideDefault === 'function'
            ? provideDefault.call(vm)
            : provideDefault
        } else if (process.env.NODE_ENV !== 'production') {
          warn(`Injection "${key}" not found`, vm)
        }
      }
    }
    return result
  }
}
```

首先通过`resolveInject`去通过source.$parent一直向父级找，直到找到对应的_provided，再赋值。若找不到赋值default。      

返回数据result后用`defineReactive`监听数据，实现响应式对象。