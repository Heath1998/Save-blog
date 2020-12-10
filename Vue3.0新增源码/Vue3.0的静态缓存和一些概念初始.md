##   Vue3.0的静态缓存和一些概念初始



```javascript
export const enum ShapeFlags {
  ELEMENT = 1,
  FUNCTIONAL_COMPONENT = 1 << 1,
  STATEFUL_COMPONENT = 1 << 2,
  TEXT_CHILDREN = 1 << 3,
  ARRAY_CHILDREN = 1 << 4,
  SLOTS_CHILDREN = 1 << 5,
  TELEPORT = 1 << 6,
  SUSPENSE = 1 << 7,
  COMPONENT_SHOULD_KEEP_ALIVE = 1 << 8,
  COMPONENT_KEPT_ALIVE = 1 << 9,
  COMPONENT = ShapeFlags.STATEFUL_COMPONENT | ShapeFlags.FUNCTIONAL_COMPONENT
}




export const enum PatchFlags {
  // 动态文字内容
  TEXT = 1,

  // 动态 class
  CLASS = 1 << 1,

  // 动态样式
  STYLE = 1 << 2,

  // 动态 props
  PROPS = 1 << 3,

  // 有动态的key，也就是说props对象的key不是确定的
  FULL_PROPS = 1 << 4,

  // 合并事件
  HYDRATE_EVENTS = 1 << 5,

  // children 顺序确定的 fragment
  STABLE_FRAGMENT = 1 << 6,

  // children中有带有key的节点的fragment
  KEYED_FRAGMENT = 1 << 7,

  // 没有key的children的fragment
  UNKEYED_FRAGMENT = 1 << 8,

  // 只有非props需要patch的，比如`ref`
  NEED_PATCH = 1 << 9,

  // 动态的插槽
  DYNAMIC_SLOTS = 1 << 10,

  // SPECIAL FLAGS -------------------------------------------------------------

  // 以下是特殊的flag，不会在优化中被用到，是内置的特殊flag

  // 表示他是静态节点，他的内容永远不会改变，对于hydrate的过程中，不会需要再对其子节点进行diff
  HOISTED = -1,

  // 用来表示一个节点的diff应该结束
  BAIL = -2,
}





vue3.0的transform阶段会标记child.codegenNode.patchFlag 赋值。  
  child.codegenNode = context.hoist(hoisted);用来更新，将当前child.codegenNode推入。


```





```javascript
<div>
  <span @click="handleClick">
    {{msg}}
  </span>
</div>

vue3.0还加入了时间缓存即cacheHandler。
如当没有开启,如下代码：

export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("p", { onClick: _ctx.handleClick }, "静态代码", 8 /* PROPS */, ["onClick"])
  ]))
}
 



如果开启了如下
export function render(_ctx, _cache) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("p", {
      onClick: _cache[1] || (_cache[1] = ($event, ...args) => (_ctx.handleClick($event, ...args)))
    }, "静态代码")
  ]))
}

为什么加入事件缓存就是创建了一个内联函数并保存，如果当前组件更新，执行render()获取新的vnode，因为缓存了所以直接用。    
假设当前handleClick改变事件，则在没内联的情况下是会重新更新当前组件的，但是如果是缓存的话，不会修改当前组件。

```







###    关于patchFlag和ShapleFlag

这两个patchFlag是在transform翻译时生成的，最后生成字符串形式的时候就存在patchFlag即

```javascript
export function render(_ctx, _cache, $props, $setup, $data, $options) {
  return (_openBlock(), _createBlock("div", null, [
    _createVNode("span", { onClick: _ctx.onClick }, _toDisplayString(_ctx.msg), 9 /* TEXT, PROPS */, ["onClick"])
  ]))
}

```

而ShapleFlag是在_createVnode生成的如下

```javascript
  // encode the vnode type information into a bitmap
  const shapeFlag = isString(type)
    ? ShapeFlags.ELEMENT
    : __FEATURE_SUSPENSE__ && isSuspense(type)
      ? ShapeFlags.SUSPENSE
      : isTeleport(type)
        ? ShapeFlags.TELEPORT
        : isObject(type)
          ? ShapeFlags.STATEFUL_COMPONENT
          : isFunction(type)
            ? ShapeFlags.FUNCTIONAL_COMPONENT
            : 0
```

