#  执行vue3.0的createApp().mount()初始化流程简述



> Vue3.0与Vue2.X的区别主要采取了Composition API的形式，当是内部又存在着许多性能上的优化，接下来让我们看看它是具体的操作流程。



##  初始例子

```javascript
<body>
    <div id="app"></div>
    <script>
 
        const {
            createApp,
            reactive,
            computed
        } = Vue
        const MyComponent = {
            template: `
                <button @click="click">
                {{ state.message }}
                </button>
            `,
            setup() {
                const state = reactive({
                    message:'Hello Vue 3!!'
                })
                function click() {
                    state.message = state.message.split('').reverse().join('')
                }
                return {
                    state,
                    click
                }
            }
        }
        createApp(MyComponent).mount( '#app')
    </script>
</body>
```

假设上方是基础代码



##  ensureRenderer的渲染器

从createApp开始

```javascript
const createApp = ((...args) => {
  // 创建 app 对象
  const app = ensureRenderer().createApp(...args)
  const { mount } = app
  // 重写 mount 方法
  app.mount = (containerOrSelector) => {
    // ...
  }
  return app
})
```



主要是通过ensureRenderer创建一个渲染器，之后通过返回一个对象包含着createApp函数。       

主要做了两点：

* 创建`app`对象
* 重写`app.mount`方法



###    ensureRenderer内部实现

```javascript
// 渲染相关的一些配置，比如更新属性的方法，操作 DOM 的方法
const rendererOptions = {
  patchProp,
  ...nodeOps
}
let renderer

function ensureRenderer() {
  return renderer || (renderer = createRenderer(rendererOptions))
}
function createRenderer(options) {
  return baseCreateRenderer(options)
}
function baseCreateRenderer(options) {
  function render(vnode, container) {
    // 组件渲染的核心逻辑
  }
  return {
    render,
    createApp: createAppAPI(render)
  }
}
```



可以看到主要是执行`baseCreateTRenderer`这里面封装了一堆patch更新函数，最后返回一个对象，包含着render函数和createApp函数。



###  createAppAPI内部

creatApp函数主要是通过createAppAPI创建的

```javascript
function createAppAPI(render) {
  // createApp createApp 方法接受的两个参数：根组件的对象和 prop
  return function createApp(rootComponent, rootProps = null) {
    const app = {
      _component: rootComponent,
      _props: rootProps,
      mount(rootContainer) {
        // 创建根组件的 vnode
        const vnode = createVNode(rootComponent, rootProps)
        // 利用渲染器渲染 vnode
        render(vnode, rootContainer)
        app._container = rootContainer
        return vnode.component.proxy
      }
    }
    return app
  }
}
```

主要返回一个方法createApp方法，此方法若执行，返回一个app对象。这个app对象是根组件的实例



##  app.mount重写

createApp返回的`app`对象中已经有这个app了，为什么还要重写入口？       

createAppApi中返回的app中的app.mount函数是一个标准的渲染方法，不包含任何的平台逻辑和规范。

```javascript
mount(rootContainer) {
  // 创建根组件的 vnode
  const vnode = createVNode(rootComponent, rootProps)
  // 利用渲染器渲染 vnode
  render(vnode, rootContainer)
  app._container = rootContainer
  return vnode.component.proxy
}
```

所以应该在外部重写

```javascript
app.mount = (containerOrSelector) => {
  // 标准化容器
  const container = normalizeContainer(containerOrSelector)
  if (!container)
    return
  const component = app._component
   // 如组件对象没有定义 render 函数和 template 模板，则取容器的 innerHTML 作为组件模板内容
  if (!isFunction(component) && !component.render && !component.template) {
    component.template = container.innerHTML
  }
  // 挂载前清空容器内容
  container.innerHTML = ''
  // 真正的挂载
  return mount(container)
}
```

如果没模板会以innerHTML中的内容作为模板，兼容2.0     

优势

* 兼容vue2.0。
* 既可以传dom，也可以传字符串。



##    createVnode内部流程

```javascript
// packages/runtime-core/src/apiCreateApp.ts
mount(rootContainer: HostElement, isHydrate?: boolean): any {
  if (!isMounted) {
    const vnode = createVNode(
      rootComponent as ConcreteComponent,
      rootProps
    )
    // store app context on the root VNode.
    // this will be set on the root instance on initial mount.
    vnode.appContext = context

    render(vnode, rootContainer)
    
    isMounted = true
    app._container = rootContainer
    // for devtools and telemetry
    ;(rootContainer as any).__vue_app__ = app

    return vnode.component!.proxy
  }
}

```



可以看到执行`mount(rootContainer)`挂载在根容器，一般是在`id='app'`容器上。这里执行了 `createVNode`根组件。

```javascript
export function createVNode(
  type: VNodeTypes,
  props: { [key: string]: any } | null = null,
  children: unknown = null,
  patchFlag: number = 0,
  dynamicProps: string[] | null = null
): VNode {
  // class和style标准化
  // class & style normalization.
  if (props !== null) {
    // for reactive or proxy objects, we need to clone it to enable mutation.
    if (isReactive(props) || SetupProxySymbol in props) {
      // proxy对象还原成源对象，extend类似于Object.assign
      props = extend({}, props)
    }
    let { class: klass, style } = props
    if (klass != null && !isString(klass)) {
      props.class = normalizeClass(klass)
    }
    if (style != null) {
      // reactive state objects need to be cloned since they are likely to be
      // mutated
      if (isReactive(style) && !isArray(style)) {
        style = extend({}, style)
      }
      props.style = normalizeStyle(style)
    }
  }
  
  // 判断vnode类型
  // encode the vnode type information into a bitmap
  const shapeFlag = isString(type)
    ? ShapeFlags.ELEMENT
    : __FEATURE_SUSPENSE__ && isSuspenseType(type)
      ? ShapeFlags.SUSPENSE
      : isObject(type)
        ? ShapeFlags.STATEFUL_COMPONENT
        : isFunction(type)
          ? ShapeFlags.FUNCTIONAL_COMPONENT
          : 0

  const vnode: VNode = {
    _isVNode: true, // 用来判断是否是vnode
    type,
    props,
    key: (props !== null && props.key) || null,
    ref: (props !== null && props.ref) || null,
    children: null,
    component: null,
    suspense: null,
    dirs: null,
    el: null,
    anchor: null,
    target: null,
    shapeFlag,
    patchFlag,
    dynamicProps,
    dynamicChildren: null,
    appContext: null
  }

  // 将children挂到vnode.children上
  normalizeChildren(vnode, children)

  // ...

  return vnode
}
```

这是创建根组件的vnode，并且赋值`shapeFlag`



##   render(vnode, rootContainer)流程

之后app.mount执行`render(vnode, rootContainer)`渲染，vnode是上面获取的根组件的vnode



```javascript
 const render: RootRenderFunction<
    HostNode,
    HostElement & {
      _vnode: HostVNode | null
    } > = (vnode, container) => {
    if (vnode == null) {
      if (container._vnode) {
        // !vnode && container上已挂载vnode，卸载vnode
        unmount(container._vnode, null, null, true)
      }
    } else {
      // 核心函数patch，将vnode挂载到container上
      patch(container._vnode || null, vnode, container)
    }
    flushPostFlushCbs()
    container._vnode = vnode
  }


```

这个逻辑显而易见，会到patch中，即`patch(container._vnode || null, vnode, container)`



##  patch

> ```
> patch`用于比较两个`vnode`的不同，将差异部分已打补丁的形式更新到页面上。`mount`过程时`n1为null`。本例直接挂载的类型是`COMPONENT`，核心函数为`processComponent
> ```



```javascript
function patch(
    n1: HostVNode | null, // null means this is a mount
    n2: HostVNode,
    container: HostElement,
    anchor: HostNode | null = null,
    parentComponent: ComponentInternalInstance | null = null,
    parentSuspense: HostSuspenseBoundary | null = null,
    isSVG: boolean = false,
    optimized: boolean = false
  ) {
    // patching & not same type, unmount old tree
    // 如果tag类型不同，卸载老的tree
    if (n1 != null && !isSameType(n1, n2)) {
      anchor = getNextHostNode(n1)
      unmount(n1, parentComponent, parentSuspense, true)
      n1 = null
    }

    const { type, shapeFlag } = n2
    switch (type) {
      case Text:
        processText(n1, n2, container, anchor)
        break
      case Comment:
        processCommentNode(n1, n2, container, anchor)
        break
      case Fragment:
        processFragment(
          n1,
          n2,
          container,
          anchor,
          parentComponent,
          parentSuspense,
          isSVG,
          optimized
        )
        break
      case Portal:
        processPortal(
          n1,
          n2,
          container,
          anchor,
          parentComponent,
          parentSuspense,
          isSVG,
          optimized
        )
        break
      default:
        if (shapeFlag & ShapeFlags.ELEMENT) {
          processElement(
            n1,
            n2,
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG,
            optimized
          )
        } else if (shapeFlag & ShapeFlags.COMPONENT) {
          // 本例挂载的是component
          processComponent(
            n1,
            n2,
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG,
            optimized
          )
        } else if (__FEATURE_SUSPENSE__ && shapeFlag & ShapeFlags.SUSPENSE) {
          ;(type as typeof SuspenseImpl).process(
            n1,
            n2,
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG,
            optimized,
            internals
          )
        } else if (__DEV__) {
          warn('Invalid HostVNode type:', n2.type, `(${typeof n2.type})`)
        }
    }
  }

```



这里主要是通过`processComponent`函数挂载



##   processComponent

> 判断挂载，调用函数`mountComponent`



```javascript
function processComponent(
    n1: HostVNode | null,
    n2: HostVNode,
    container: HostElement,
    anchor: HostNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: HostSuspenseBoundary | null,
    isSVG: boolean,
    optimized: boolean
  ) {
    if (n1 == null) {
      if (n2.shapeFlag & ShapeFlags.STATEFUL_COMPONENT_KEPT_ALIVE) {
        ;(parentComponent!.sink as KeepAliveSink).activate(
          n2,
          container,
          anchor
        )
      } else {
        // 核心函数mountComponent
        mountComponent(
          n2,
          container,
          anchor,
          parentComponent,
          parentSuspense,
          isSVG
        )
      }
    } else {
      // ...
  }

```

具体执行`mountComponent`函数



##  mountComponent

> 两个核心函数，`setupStatefulComponent`和`setupRenderEffect`。 `setupStatefulComponent`: 执行`setup`，将返回值`setupResult`转换为`reactive`数据。将`template`转化成`render`函数。`setupRenderEffect`: 调用`effect`，`render`渲染页面



```javascript
function mountComponent(
    initialVNode: HostVNode,
    container: HostElement,
    anchor: HostNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: HostSuspenseBoundary | null,
    isSVG: boolean
  ) {
    // 创建当前组件实例
    const instance: ComponentInternalInstance = (initialVNode.component = createComponentInstance(
      initialVNode,
      parentComponent
    ))

    if (__DEV__) {
      pushWarningContext(initialVNode)
    }

    const Comp = initialVNode.type as Component

    // inject renderer internals for keepAlive
    if ((Comp as any).__isKeepAlive) {
      const sink = instance.sink as KeepAliveSink
      sink.renderer = internals
      sink.parentSuspense = parentSuspense
    }

    // resolve props and slots for setup context
    const propsOptions = Comp.props
    resolveProps(instance, initialVNode.props, propsOptions)
    resolveSlots(instance, initialVNode.children)

    // setup stateful logic
    if (initialVNode.shapeFlag & ShapeFlags.STATEFUL_COMPONENT) {
      <!--执行setup-->
      setupStatefulComponent(instance, parentSuspense)
    }

    // setup() is async. This component relies on async logic to be resolved
    // before proceeding
    if (__FEATURE_SUSPENSE__ && instance.asyncDep) {
      if (!parentSuspense) {
        if (__DEV__) warn('async setup() is used without a suspense boundary!')
        return
      }

      parentSuspense.registerDep(instance, setupRenderEffect)

      // give it a placeholder
      const placeholder = (instance.subTree = createVNode(Comment))
      processCommentNode(null, placeholder, container, anchor)
      initialVNode.el = placeholder.el
      return
    }
    
    // 默认调用一次effect渲染页面，并将依赖存入targetMap中
    setupRenderEffect(
      instance,
      parentSuspense,
      initialVNode,
      container,
      anchor,
      isSVG
    )

    if (__DEV__) {
      popWarningContext()
    }
  }

```



这里分为两部分，前者解析，后者更新。



##   setupStatefulComponent

> 执行`setup()`。`handleSetupResult`将setup的返回值包装成响应式对象。`finishComponentSetup`将`template`编译成render函数

```javascript
export function setupStatefulComponent(
  instance: ComponentInternalInstance,
  parentSuspense: SuspenseBoundary | null
) {
  const Component = instance.type as ComponentOptions
  
  // ...
  const { setup } = Component
  if (setup) {
    const setupContext = (instance.setupContext =
      setup.length > 1 ? createSetupContext(instance) : null)

    currentInstance = instance
    currentSuspense = parentSuspense
    
    <!-- 执行setup(),可以看出给setup传了两个参数，propsProxy和setupContext-->
    const setupResult = callWithErrorHandling(
      setup,
      instance,
      ErrorCodes.SETUP_FUNCTION,
      [propsProxy, setupContext]
    )
    currentInstance = null
    currentSuspense = null

    if (isPromise(setupResult)) {
      if (__FEATURE_SUSPENSE__) {
        // async类型的setup处理方式
        // async setup returned Promise.
        // bail here and wait for re-entry.
        instance.asyncDep = setupResult
      } else if (__DEV__) {
        warn(
          `setup() returned a Promise, but the version of Vue you are using ` +
            `does not support it yet.`
        )
      }
      return
    } else {
    
      <!-- 将setup的返回值包装成响应式对象 -->
      handleSetupResult(instance, setupResult, parentSuspense)
    }
  } else {
    finishComponentSetup(instance, parentSuspense)
  }
}


```

这里主要执行`handleSetupResult(instance, setupResult, parentSuspense)`这个函数里面最后会执行`finishComponentSetup(instance, parentSuspense) `将template编译为render函数



###  handleSetupResult处理setup生成响应式数据

```javascript
export function handleSetupResult(
  instance: ComponentInternalInstance,
  setupResult: unknown,
  parentSuspense: SuspenseBoundary | null
) {
  if (isFunction(setupResult)) {
    // setup returned an inline render function
    instance.render = setupResult as RenderFunction
  } else if (isObject(setupResult)) {
    if (__DEV__ && isVNode(setupResult)) {
      warn(
        `setup() should not return VNodes directly - ` +
          `return a render function instead.`
      )
    }
    // setup returned bindings.
    // assuming a render function compiled from template is present.
    // setupResult就是setup函数中return的值
    // 将setupResult包装成proxy对象
    instance.renderContext = reactive(setupResult)
  } else if (__DEV__ && setupResult !== undefined) {
    warn(
      `setup() should return an object. Received: ${
        setupResult === null ? 'null' : typeof setupResult
      }`
    )
  }
  <!--将template编译为render函数-->
  finishComponentSetup(instance, parentSuspense)
}

```

这里主要是将`setup()`的结果在包装成Proxy对象。之后执行template编译为render函数



###   finishComponentSetup执行编译

```javascript
function finishComponentSetup(
  instance: ComponentInternalInstance,
  parentSuspense: SuspenseBoundary | null
) {
  const Component = instance.type as ComponentOptions
  if (!instance.render) {
    if (__RUNTIME_COMPILE__ && Component.template && !Component.render) {
      // __RUNTIME_COMPILE__ ensures `compile` is provided
      // 将template转化为render函数
      // 在vue.ts中注册了compile函数
      Component.render = compile!(Component.template, {
        isCustomElement: instance.appContext.config.isCustomElement || NO,
        onError(err: CompilerError) {
          if (__DEV__) {
            const message = `Template compilation error: ${err.message}`
            const codeFrame =
              err.loc &&
              generateCodeFrame(
                Component.template!,
                err.loc.start.offset,
                err.loc.end.offset
              )
            warn(codeFrame ? `${message}\n${codeFrame}` : message)
          }
        }
      })
    }
    if (__DEV__ && !Component.render) {
      /* istanbul ignore if */
      if (!__RUNTIME_COMPILE__ && Component.template) {
        warn(
          `Component provides template but the build of Vue you are running ` +
            `does not support on-the-fly template compilation. Either use the ` +
            `full build or pre-compile the template using Vue CLI.`
        )
      } else {
        warn(
          `Component is missing${
            __RUNTIME_COMPILE__ ? ` template or` : ``
          } render function.`
        )
      }
    }
    // 将component的render赋值给外层render
    // const App = {render: h('div', 'hello world')} =>
    // instance.render = h('div', 'hello world')
    instance.render = (Component.render || NOOP) as RenderFunction
  }

  // support for 2.x options
  if (__FEATURE_OPTIONS__) {
    currentInstance = instance
    currentSuspense = parentSuspense
    applyOptions(instance, Component)
    currentInstance = null
    currentSuspense = null
  }

  if (instance.renderContext === EMPTY_OBJ) {
    instance.renderContext = reactive({})
  }
}

```

主要就是执行`Compile`进行编译。    

compiler 主要用于编译模板生成渲染函数，期间一共经历三个过程，分别是 parse、transform 和 codegen。parse 是初步将我们的模板转化成 ast，transform 是对我们生成的 ast 进行转化，包括了 nodeTransform 和 directiveTransform， 最后是 codegen， 将转化后的 ast 生成 字符串代码。     

这之中transform是会计算`patchFlag`的.



##  setupRenderEffect

> 使用effect包裹更新函数，这样响应式数据改变，会造成更新函数的执行

```javascript
function setupRenderEffect(
    instance: ComponentInternalInstance,
    parentSuspense: HostSuspenseBoundary | null,
    initialVNode: HostVNode,
    container: HostElement,
    anchor: HostNode | null,
    isSVG: boolean
  ) {
    // create reactive effect for rendering
    let mounted = false
    instance.update = effect(function componentEffect() {
      if (!mounted) {
        <!--调用render函数(template编译生成)，将template转化为subTree-->
        const subTree = (instance.subTree = renderComponentRoot(instance))
        // beforeMount hook
        if (instance.bm !== null) {
          invokeHooks(instance.bm)
        }
        <!--核心函数patch，将subTree渲染到页面上-->
        patch(null, subTree, container, anchor, instance, parentSuspense, isSVG)
        initialVNode.el = subTree.el
        // mounted hook
        if (instance.m !== null) {
          queuePostRenderEffect(instance.m, parentSuspense)
        }
        mounted = true
      } else {
          // ...
      }
  }

```

这里主要是注册了一个effect包裹住的更新函数，effect会先将更新函数直接执行一遍，由于第一次`mounted`还未执行，所以跳入。先执行`renderComponentRoot(instance)`进行执行render函数获取当前组件的vnode。      

之后通过patch进行初始化更新，这里执行patch进行更新。



这里看看patch的详细操作



##  patch

这个函数是所有更新里面的重中之重，现在我们来看一下最开始的例子执行到现在的时候该怎么做。

此时`ShapeFlag = 9`

```html
<button @click="click">
{{ state.message }}
</button>
```



```javascript
  const patch: PatchFn = (
    n1,
    n2,
    container,
    anchor = null,
    parentComponent = null,
    parentSuspense = null,
    isSVG = false,
    optimized = false
  ) => {
   const { type, ref, shapeFlag } = n2
    switch (type) {
      case Text:
        processText(n1, n2, container, anchor)
        break
      case Comment:
        processCommentNode(n1, n2, container, anchor)
        break
      case Static:
        if (n1 == null) {
          mountStaticNode(n2, container, anchor, isSVG)
        } else if (__DEV__) {
          patchStaticNode(n1, n2, container, isSVG)
        }
        break
      case Fragment:
        processFragment(
          n1,
          n2,
          container,
          anchor,
          parentComponent,
          parentSuspense,
          isSVG,
          optimized
        )
        break
      default:
        if (shapeFlag & ShapeFlags.ELEMENT) {
          processElement(
            n1,
            n2,
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG,
            optimized
          )
        } 
       .......
  }
```

这里会跳到`processElement`中因为ShapeFlag等于9，ShapeFlags.ELEMENT为1.



###   processElement

```javascript
  const processElement = (
    n1: VNode | null,
    n2: VNode,
    container: RendererElement,
    anchor: RendererNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: SuspenseBoundary | null,
    isSVG: boolean,
    optimized: boolean
  ) => {
    isSVG = isSVG || (n2.type as string) === 'svg'
    if (n1 == null) {
      mountElement(
        n2,
        container,
        anchor,
        parentComponent,
        parentSuspense,
        isSVG,
        optimized
      )
    } else {
      patchElement(n1, n2, parentComponent, parentSuspense, isSVG, optimized)
    }
  }
```

这个函数主要用来判断当前是n1,即是否第一次渲染，根据不同情况执行不同的函数，mountElement是新建实例

，patchElement进行对比。



###  mountElement

```javascript
  const mountElement = (
    vnode: VNode,
    container: RendererElement,
    anchor: RendererNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: SuspenseBoundary | null,
    isSVG: boolean,
    optimized: boolean
  ) => {
    let el: RendererElement
    let vnodeHook: VNodeHook | undefined | null
    const {
      type,
      props,
      shapeFlag,
      transition,
      scopeId,
      patchFlag,
      dirs
    } = vnode
    if (
      !__DEV__ &&
      vnode.el &&
      hostCloneNode !== undefined &&
      patchFlag === PatchFlags.HOISTED
    ) {
      ......
    } else {
      // 创建真实dom节点，这里使用封装的nodeOps
      el = vnode.el = hostCreateElement(
        vnode.type as string,
        isSVG,
        props && props.is
      )
  
      // mount children first, since some props may rely on child content
      // being already rendered, e.g. `<select value>`
      // 判断子元素
      if (shapeFlag & ShapeFlags.TEXT_CHILDREN) {
        hostSetElementText(el, vnode.children as string)
      } else if (shapeFlag & ShapeFlags.ARRAY_CHILDREN) {
        mountChildren(
          vnode.children as VNodeArrayChildren,
          el,
          null,
          parentComponent,
          parentSuspense,
          isSVG && type !== 'foreignObject',
          optimized || !!vnode.dynamicChildren
        )
      }

......
      // props
// 绑定事件等
      if (props) {
        for (const key in props) {
          if (!isReservedProp(key)) {
            hostPatchProp(
              el,
              key,
              null,
              props[key],
              isSVG,
              vnode.children as VNode[],
              parentComponent,
              parentSuspense,
              unmountChildren
            )
          }
        }
        if ((vnodeHook = props.onVnodeBeforeMount)) {
          invokeVNodeHook(vnodeHook, parentComponent, vnode)
        }
      }
      // scopeId
      setScopeId(el, scopeId, vnode, parentComponent)
    }
      
  ......
  // 插入父级
    hostInsert(el, container, anchor)

    ......
  }
```

从这里开始主要是根据shapeFlag创造真实元素，添加Text文本，增加props。则button就成立了。并且会插入父级`id='app'的dev中，完成出事渲染。`