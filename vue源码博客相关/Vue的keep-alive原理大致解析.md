##   Vue的keep-alive原理大致解析

### keepalive组件

先来看看keepalive这个组件源码做了些什么？

```javascript
// 只会寻找第一个组件
export function getFirstComponentChild (children: ?Array<VNode>): ?VNode {
  if (Array.isArray(children)) {
    for (let i = 0; i < children.length; i++) {
      const c = children[i]
      if (isDef(c) && (isDef(c.componentOptions) || isAsyncPlaceholder(c))) {
        return c
      }
    }
  }
}



export default {
  name: 'keep-alive',
  abstract: true,

  props: {
    include: patternTypes,
    exclude: patternTypes,
    max: [String, Number]
  },

  created () {
    this.cache = Object.create(null)
    this.keys = []
  },

  destroyed () {
    for (const key in this.cache) {
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },

  mounted () {
    this.$watch('include', val => {
      pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
      pruneCache(this, name => !matches(val, name))
    })
  },

  render () {
    // 获取插槽的default一般是一个虚拟vnode的数组。
    const slot = this.$slots.default
    // 寻找第一个组件
    const vnode: VNode = getFirstComponentChild(slot)
    const componentOptions: ?VNodeComponentOptions = vnode && vnode.componentOptions
    if (componentOptions) {
      // check pattern
      const name: ?string = getComponentName(componentOptions)
      const { include, exclude } = this
      if (
        // not included
        (include && (!name || !matches(include, name))) ||
        // excluded
        (exclude && name && matches(exclude, name))
      ) {
        return vnode
      }

      const { cache, keys } = this
      const key: ?string = vnode.key == null
        // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key
      if (cache[key]) {
        vnode.componentInstance = cache[key].componentInstance
        // make current key freshest
        remove(keys, key)
        keys.push(key)
      } else {
        cache[key] = vnode
        keys.push(key)
        // prune oldest entry
        if (this.max && keys.length > parseInt(this.max)) {
          pruneCacheEntry(cache, keys[0], keys, this._vnode)
        }
      }

      vnode.data.keepAlive = true
    }
    return vnode || (slot && slot[0])
  }
}
```

主要逻辑都在render上，当是为缓存过的vnode节点，就缓存。如果缓存过的就直接返回缓存vnode。



### 初始渲染

当第一次渲染时，因为没有缓存直接返回就按照正常组件的渲染方式，vue初始化到patch时执行`createComponent`，再去创建keepalive实例，之后就是keepalive执行mountComponent中的update时用到了render返回的vnode，判断没有缓存，就记录一下缓存，设keepAlive为true。之后就是正常的patch函数了。



### 缓存渲染

这里就要注意了，大概有两张方式使用keep-alive

第一种

```javascript
    <keep-alive>
      <component :is="comp"></component>
     </keep-alive>
```

这种会触发包含keepalive即包含comp这个响应式数据的vue实例或vue组件实例的_update去重新patch。

第二种

```javascript
<keep-alive><router-view></router-view></keep-alive>
```

这种的话由于router会在插件注册时，在组件混入befoerCreate方法，这个方法会在根组件上响应式声明 _router属性，当路由改变，`app. _router = router`。





当触发根vue实例，会执行update更新，执行patch中会调用patchVnode进行对比，会递归调用直到对比keepAlive。

```javascript
  function patchVnode (
    oldVnode,
    vnode,
    insertedVnodeQueue,
    ownerArray,
    index,
    removeOnly
  ) {
    //........
    let i
    const data = vnode.data
    if (isDef(data) && isDef(i = data.hook) && isDef(i = i.prepatch)) {
      i(oldVnode, vnode)
    }
    //......
    }
```



这里会执行prepatch钩子函数

```javascript
  prepatch (oldVnode: MountedComponentVNode, vnode: MountedComponentVNode) {
    const options = vnode.componentOptions
    const child = vnode.componentInstance = oldVnode.componentInstance
    updateChildComponent(
      child,      // 为当前keepalive的vue组件实例
      options.propsData, // updated props
      options.listeners, // updated listeners
      vnode, // new parent vnode
      options.children // new children 当前keepalive的子vnode
    )
  },
```

这里主要是执行了`updateChildComponent`

```javascript
export function updateChildComponent (
  vm: Component,
  propsData: ?Object,
  listeners: ?Object,
  parentVnode: MountedComponentVNode,
  renderChildren: ?Array<VNode>
) {

//......

// 判断子vnode是否存在
  const hasChildren = !!(
    renderChildren ||               // has new static slots
    vm.$options._renderChildren ||  // has old static slots
    parentVnode.data.scopedSlots || // has new scoped slots
    vm.$scopedSlots !== emptyObject // has old scoped slots
  )
  
  //......
  
  if (hasChildren) {
    vm.$slots = resolveSlots(renderChildren, parentVnode.context)
    // 强制执行一次当前vm的更新
    vm.$forceUpdate()
  }
	// ......
}
```

这里主要判断hasChildren子vnode是否存在，存在就生成新的vm.$slot插值。之后执行一次当前keepAlive组件实例的更新即update。

```javascript
  Vue.prototype.$forceUpdate = function () {
    const vm: Component = this
    if (vm._watcher) {
      vm._watcher.update()
    }
  }
```

之后会去执行一次_update更新，其中会使用patch。其中 _update的vnode为当前keepalive的render函数返回，会返回缓存的vnode，之后执行patch。      

patch过程中有执行一次createComponent

```javascript
function createComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
  let i = vnode.data
  if (isDef(i)) {
    const isReactivated = isDef(vnode.componentInstance) && i.keepAlive
    if (isDef(i = i.hook) && isDef(i = i.init)) {
      i(vnode, false /* hydrating */)
    }
    // after calling the init hook, if the vnode is a child component
    // it should've created a child instance and mounted it. the child
    // component also has set the placeholder vnode's elm.
    // in that case we can just return the element and be done.
    if (isDef(vnode.componentInstance)) {
      initComponent(vnode, insertedVnodeQueue)
      insert(parentElm, vnode.elm, refElm)
      if (isTrue(isReactivated)) {
        reactivateComponent(vnode, insertedVnodeQueue, parentElm, refElm)
      }
      return true
    }
  }
}
```

这里又会执行一次init。

```javascript
const componentVNodeHooks = {
  init (vnode: VNodeWithData, hydrating: boolean): ?boolean {
    if (
      vnode.componentInstance &&
      !vnode.componentInstance._isDestroyed &&
      vnode.data.keepAlive
    ) {
      // kept-alive components, treat as a patch
      const mountedNode: any = vnode // work around flow
      componentVNodeHooks.prepatch(mountedNode, mountedNode)
    } else {
      const child = vnode.componentInstance = createComponentInstanceForVnode(
        vnode,
        activeInstance
      )
      child.$mount(hydrating ? vnode.elm : undefined, hydrating)
    }
  },
}
```

这里由于是缓存的vnode，就会直接再执行一次`prepatch(vnode)`执行pre又和上面一样了，强制更新当前传入的子组件。这时将不再走 $mount 的逻辑，只调用 prepatch 更新实例属性。所以在缓存组件被激活时，不会执行 created 和 mounted 的生命周期函数。      

回到 createComponent，此时的 isReactivated 为 true，调用 reactivateComponent:

```javascript
function reactivateComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
  let i
  // hack for #4339: a reactivated component with inner transition
  // does not trigger because the inner node's created hooks are not called
  // again. It's not ideal to involve module-specific logic in here but
  // there doesn't seem to be a better way to do it.
  let innerNode = vnode
  while (innerNode.componentInstance) {
    innerNode = innerNode.componentInstance._vnode
    if (isDef(i = innerNode.data) && isDef(i = i.transition)) {
      for (i = 0; i < cbs.activate.length; ++i) {
        cbs.activate[i](emptyNode, innerNode)
      }
      insertedVnodeQueue.push(innerNode)
      break
    }
  }
  // unlike a newly created component,
  // a reactivated keep-alive component doesn't insert itself
  insert(parentElm, vnode.elm, refElm)
}
```

最后调用 insert 插入组件的dom节点，至此缓存渲染流程完成。

**小结**：组件首次渲染时，keep-alive 会将组件缓存起来。等到缓存渲染时，keep-alive 会更新插槽内容，之后 $forceUpdate 重新渲染。这样在 render 时就获取到最新的组件，如果命中缓存则从缓存中返回 VNode。







