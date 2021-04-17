## vue深入component源码过程操作



> 就是调用了Vue.extend（）并将组件注册到全局。

### Vue.component的注册





先来看一下Vue.component是怎么暴露在外面的



```javascript
export const ASSET_TYPES = [
  'component',
  'directive',
  'filter'
]

export function initAssetRegisters (Vue: GlobalAPI) {
  /**
   * Create asset registration methods.
   */
  ASSET_TYPES.forEach(type => {
    Vue[type] = function (
      id: string,
      definition: Function | Object
    ): Function | Object | void {
      if (!definition) {
        return this.options[type + 's'][id]
      } else {
        /* istanbul ignore if */
        if (process.env.NODE_ENV !== 'production' && type === 'component') {
          validateComponentName(id)
        }
        if (type === 'component' && isPlainObject(definition)) {
          definition.name = definition.name || id
          // 就是执行Vue.extend，_base指向自己Vue，返回一个继承Vue的子类
          definition = this.options._base.extend(definition)
        }
        if (type === 'directive' && typeof definition === 'function') {
          definition = { bind: definition, update: definition }
        }
      	// 如果为components在options存储子类
        this.options[type + 's'][id] = definition
        return definition
      }
    }
  })
}
```



```javascript
var b = Vue.component('b-component', {
  props: {
    size: {
      type: String,
      default: ''
    }
  },
  template:'<div>111111111</div>'
})
```



如果输出Vue函数，结果如下：

```javascript
options: {
  components: {KeepAlive: {…}, Transition: {…}, TransitionGroup: {…}, b-component: ƒ       directives: {model: {…}, show: {…}}
  filters: {}
  _base: ƒ Vue(options)
}
```

上方包含着新建的组件子类b-component



###  Vue.extednd的合并后子类的options



```javascript
  Vue.extend = function (extendOptions: Object): Function {
    extendOptions = extendOptions || {}
    const Super = this
    const SuperId = Super.cid
    const cachedCtors = extendOptions._Ctor || (extendOptions._Ctor = {})
    if (cachedCtors[SuperId]) {
      return cachedCtors[SuperId]
    }

    const name = extendOptions.name || Super.options.name
    if (process.env.NODE_ENV !== 'production' && name) {
      validateComponentName(name)
    }

    const Sub = function VueComponent (options) {
      this._init(options)
    }
    
    // 继承
    Sub.prototype = Object.create(Super.prototype)
    Sub.prototype.constructor = Sub
    Sub.cid = cid++
    // 合并主要
    Sub.options = mergeOptions(
      Super.options,
      extendOptions
    )
    Sub['super'] = Super

    // For props and computed properties, we define the proxy getters on
    // the Vue instances at extension time, on the extended prototype. This
    // avoids Object.defineProperty calls for each instance created.
    if (Sub.options.props) {
      initProps(Sub)
    }
    if (Sub.options.computed) {
      initComputed(Sub)
    }

    // allow further extension/mixin/plugin usage
    Sub.extend = Super.extend
    Sub.mixin = Super.mixin
    Sub.use = Super.use

    // create asset registers, so extended classes
    // can have their private assets too.
    ASSET_TYPES.forEach(function (type) {
      Sub[type] = Super[type]
    })
    // enable recursive self-lookup
    // 设置当前的组件为自己子类
    if (name) {
      Sub.options.components[name] = Sub
    }

    // keep a reference to the super options at extension time.
    // later at instantiation we can check if Super's options have
    // been updated.
    Sub.superOptions = Super.options
    Sub.extendOptions = extendOptions
    Sub.sealedOptions = extend({}, Sub.options)

    // cache constructor
    // 设置_Ctor为自己
    cachedCtors[SuperId] = Sub
    return Sub
  }
}
```



###  mergeOptions合并



主要逻辑是继承父类，并且做了一次options合并，并且赋给自己。      

合并操作如下：



```javascript
export function mergeOptions (
  parent: Object,
  child: Object,
  vm?: Component
): Object {
  if (process.env.NODE_ENV !== 'production') {
    checkComponents(child)
  }

  if (typeof child === 'function') {
    child = child.options
  }

  // 规范化各种变量
  normalizeProps(child, vm)
  normalizeInject(child, vm)
  normalizeDirectives(child)

  // Apply extends and mixins on the child options,
  // but only if it is a raw options object that isn't
  // the result of another mergeOptions call.
  // Only merged options has the _base property.
  if (!child._base) {
    if (child.extends) {
      parent = mergeOptions(parent, child.extends, vm)
    }
    if (child.mixins) {
      for (let i = 0, l = child.mixins.length; i < l; i++) {
        parent = mergeOptions(parent, child.mixins[i], vm)
      }
    }
  }

  // 主体操作
  const options = {}
  let key
  for (key in parent) {
    mergeField(key)
  }
  for (key in child) {
    if (!hasOwn(parent, key)) {
      mergeField(key)
    }
  }
  function mergeField (key) {
    const strat = strats[key] || defaultStrat
    options[key] = strat(parent[key], child[key], vm, key)
  }
  return options
}
```



主要操作都在最下面，就是遍历parent和child，并且都执行mergeField函数。具体看看strats和defaultStart是些什么？



```javascript
const defaultStrat = function (parentVal: any, childVal: any): any {
  return childVal === undefined
    ? parentVal
    : childVal
}
```

`defaultStrat`函数主要逻辑为判断子数据是否存在，存在返回子数据，不存在返回父数据。    

那接下来看看strats[key]的逻辑，根据key值的不同，会执行生命周期/compones/directive/的类型。



####  钩子函数hook



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
```



用图片表示返回逻辑



![https://segmentfault.com/img/bV9V15?w=810&h=337/view](https://segmentfault.com/img/bV9V15?w=810&h=337/view)

(1) child options上不存在该属性，parent options上存在,则返回parent上的属性。



![图片描述](https://segmentfault.com/img/bV9V5s?w=769&h=340)



（2）child和parent都存在该属性，则返回concat之后的属性

![图片描述](https://segmentfault.com/img/bV9V81?w=797&h=384)

(3) child上存在该属性，parent不存在，且child上的该属性是Array，则直接返回child上的该属性



![图片描述](https://segmentfault.com/img/bV9V8L?w=760&h=365)

(4) child上存在该属性，parent不存在，且child上的该属性不是Array，则把该属性先转换成Array,再返回。



#### props/methods/inject/computed的策略



```
strats.props =
strats.methods =
strats.inject =
strats.computed = function (
  parentVal,
  childVal,
  vm,
  key
) {
  if (childVal && "development" !== 'production') {
    assertObjectType(key, childVal, vm);
  }
  if (!parentVal) { return childVal }
  var ret = Object.create(null);
  extend(ret, parentVal);
  if (childVal) { extend(ret, childVal); }
  return ret
};

function extend (to, _from) {
  for (var key in _from) {
    to[key] = _from[key];
  }
  return to
}
```



(1) 如果parent option上没有该属性，返回child option的属性     

(2) 如果parent option属性和child option属性都有 返回一个合并的ret属性，其中child的属性会替换掉parent的属性。      

(3) 如果parent option属性有，child option属性没有，返回一个新对象ret原型链指向null





####  components/directives/filters的合并策略

```javascript
function mergeAssets (
  parentVal: ?Object,
  childVal: ?Object,
  vm?: Component,
  key: string
): Object {
  const res = Object.create(parentVal || null)
  if (childVal) {
    process.env.NODE_ENV !== 'production' && assertObjectType(key, childVal, vm)
    return extend(res, childVal)
  } else {
    return res
  }
}
```



先设置res 为一个空对象，原型链为指向parent 的option中的属性。如果不存在，指向null。

(1) 如果child option 中的属性存在，返回合并的res。

(2 如果child option中属性不存在，直接返回res。





### 当设置全局组件，显示options情况



```javascript
var b = Vue.component('b-component', {
  props: {
    size: {
      type: String,
      default: ''
    }
  },
  template:'<div>111111111</div>'
})
```

设置的全局组件       

则b继承的子构造函数的options属性如下：



```javascript
options: {
    components: {b-component: ƒ}
    directives: {}
    filters: {}
    name: "b-component"
    props: {size: {…}}
    template: "<div>111111111</div>"
    _Ctor: {0: ƒ}
    _base: ƒ Vue(options)
    __proto__: Object
}
```



符合执行Vue.component应该的结果。



###  组件从render到生成Vnode的过程



首先明白是解析html生成ast树再生成render字符串，再通过执行字符串的_c()函数去创建Vnode虚拟dom。



```javascript
// 内部执行将render函数转化为Vnode的函数
function _createElement(context,tag,data,children,normalizationType) {
  ···
  if (typeof tag === 'string') {
    // 子节点的标签为普通的html标签，直接创建Vnode
    if (config.isReservedTag(tag)) {
      vnode = new VNode(
        config.parsePlatformTagName(tag), data, children,
        undefined, undefined, context
      );
    // 子节点标签为注册过的组件标签名，则子组件Vnode的创建过程
    } else if ((!data || !data.pre) && isDef(Ctor = resolveAsset(context.$options, 'components', tag))) {
      // 创建子组件Vnode
      vnode = createComponent(Ctor, data, context, children, tag);
    }
  }
}
```

当执行_c函数时会执行 ` _createElement` 函数，此函数再根据是普通html标签，还是组件标签，来决定如何创建虚拟dom。

 

`resolveAsset(context.$options, 'components', tag)` 会检查组件是否注册，和组件名规范。



```javascript
 // 创建子组件过程
  function createComponent (
    Ctor, // 子类构造器
    data,
    context, // vm实例
    children, // 子节点
    tag // 子组件占位符
  ) {
    ···
    // Vue.options里的_base属性存储Vue构造器
    var baseCtor = context.$options._base;

    // 针对局部组件注册场景
    if (isObject(Ctor)) {
      Ctor = baseCtor.extend(Ctor);
    }
    data = data || {};
    // 构造器配置合并
    resolveConstructorOptions(Ctor);
    // 挂载组件钩子
    installComponentHooks(data);

    // return a placeholder vnode
    var name = Ctor.options.name || tag;
    // 创建子组件vnode，名称以 vue-component- 开头
    var vnode = new VNode(("vue-component-" + (Ctor.cid) + (name ? ("-" + name) : '')),data, undefined, undefined, undefined, context,{ Ctor: Ctor, propsData: propsData, listeners: listeners, tag: tag, children: children },asyncFactory);

    return vnode
  }

```

当`new VNode`时并不会去创立一个真实element元素也不会去创建extend继承的子类，它只会赋值到vnode节点上。



注意installComponentsHooks挂载钩子函数。   

他会合并钩子函数到data.hook上，当传入new Vnode时会赋值。



![vnode父子组件](C:\drawIo图片流程图\学习图片\vnode父子组件.png)





### VNode组件真实节点生成



视图更新主要在_update函数上进行，它有两种情况执行，一种是刚初始化，一种是使用的响应式数据发生改变。           

之后在第一次执行初始化时， 再执行patch函数中。



```javascript
function patch (oldVnode, vnode, hydrating, removeOnly) {
    if (isUndef(vnode)) {
      if (isDef(oldVnode)) invokeDestroyHook(oldVnode)
      return
    }

    let isInitialPatch = false
    const insertedVnodeQueue = []

    if (isUndef(oldVnode)) {
      // empty mount (likely as component), create new root element
      isInitialPatch = true
      createElm(vnode, insertedVnodeQueue)       // 初始创建
    } else {
      const isRealElement = isDef(oldVnode.nodeType)
      if (!isRealElement && sameVnode(oldVnode, vnode)) {
        // patch existing root node
        patchVnode(oldVnode, vnode, insertedVnodeQueue, null, null, removeOnly)
      }
      ......................
   }
```



如上所示初始化就会执行createElm中



```javascript
// 创建真实dom
function createElm (vnode,insertedVnodeQueue,parentElm,refElm,nested,ownerArray,index) {
  ···
  // 递归创建子组件真实节点,直到完成所有子组件的渲染才进行根节点的真实节点插入
  if (createComponent(vnode, insertedVnodeQueue, parentElm, refElm)) {
    return
  }
  ···
  // 创建真实dom节点
  vnode.elm = vnode.ns
      ? nodeOps.createElementNS(vnode.ns, tag)
  : nodeOps.createElement(tag, vnode)
  
  var children = vnode.children;
  // 遍历childern，会遍历执行createElm
  createChildren(vnode, children, insertedVnodeQueue);
  ···
  insert(parentElm, vnode.elm, refElm);
}





function createChildren(vnode, children, insertedVnodeQueue) {
  for (var i = 0; i < children.length; ++i) {
    // 遍历子节点，递归调用创建真实dom节点的方法 - createElm
    createElm(children[i], insertedVnodeQueue, vnode.elm, null, true, children, i);
  }
}

```



`createElm`会判断vnode是否为组件节点，如果不是会创建当前标签节点，之后遍历当前vnode的子元素。子元素又会执行`createElm`这样就会建立实例组件。



接下来看看`createComponent`究竟做了什么？



```javascript
  function createComponent (vnode, insertedVnodeQueue, parentElm, refElm) {
    let i = vnode.data
    if (isDef(i)) {
      const isReactivated = isDef(vnode.componentInstance) && i.keepAlive
      if (isDef(i = i.hook) && isDef(i = i.init)) {
        i(vnode, false /* hydrating */, parentElm, refElm)
      }
      // after calling the init hook, if the vnode is a child component
      // it should've created a child instance and mounted it. the child
      // component also has set the placeholder vnode's elm.
      // in that case we can just return the element and be done.
      if (isDef(vnode.componentInstance)) {
        initComponent(vnode, insertedVnodeQueue)
        if (isTrue(isReactivated)) {
          reactivateComponent(vnode, insertedVnodeQueue, parentElm, refElm)
        }
        return true
      }
    }
  }
```



它这里会初始化去执行之前挂载的init，在`installComponentHooks(data)`挂载上的init，这里看一下init具体做了什么？



```javascript
  init (
    vnode: VNodeWithData,
    hydrating: boolean,
    parentElm: ?Node,
    refElm: ?Node
  ): ?boolean {
    if (
      vnode.componentInstance &&
      !vnode.componentInstance._isDestroyed &&
      vnode.data.keepAlive
    ) {
      // kept-alive components, treat as a patch
      const mountedNode: any = vnode // work around flow
      componentVNodeHooks.prepatch(mountedNode, mountedNode)
    } else {
      // 创建一个子组建实例
      const child = vnode.componentInstance = createComponentInstanceForVnode(
        vnode,
        activeInstance,
        parentElm,
        refElm
      )
      child.$mount(hydrating ? vnode.elm : undefined, hydrating)
    }
  },
```

这里会判断`componentInstance`子组件实例是否存在，不存在就new一个子组件实例。看看`createComponentInstanceForVnode`做了什么？



```javascript
export function createComponentInstanceForVnode (
  vnode: any, // we know it's MountedComponentVNode but flow doesn't
  parent: any, // activeInstance in lifecycle state
  parentElm?: ?Node,
  refElm?: ?Node
): Component {
  const options: InternalComponentOptions = {
    _isComponent: true,
    parent,
    _parentVnode: vnode,
    // 注意这里获取父级标签元素实例，子组建执行_update时，会插入父级元素
    _parentElm: parentElm || null,
    _refElm: refElm || null
  }
  // check inline-template render functions
  const inlineTemplate = vnode.data.inlineTemplate
  if (isDef(inlineTemplate)) {
    options.render = inlineTemplate.render
    options.staticRenderFns = inlineTemplate.staticRenderFns
  }
  return new vnode.componentOptions.Ctor(options);
}
```

这里构建了一个`options`声明了各种基本属性，注意_parentElm，会在        ` _update`时插入父级元素。并且以option为参数，new一个子组建实例。      

之后执行`child.$mount(hydrating ? vnode.elm : undefined, hydrating)`,这里是挂载但也有判断



```javascript
// public mount method
// /web/runtime/index.js
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}


// /web/entry-runtime-with-compiler.js
import Vue from './runtime/index'

const mount = Vue.prototype.$mount
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && query(el)


  const options = this.$options;
  return mount.call(this, el, hydrating)
}
```



这里可以理解为`mount `等于/runtime/index.js中的函数。$mount执行到`mount.call(this, el, hydrating)`再执行`mountComponent(this, el, hydrating)`,这里就开始执行` _update(_render(), hydrating)`去更新视图，创建标签实例，插入父级真实elm元素标签。



总的来说挂载子组建标签元素即执行mountComponent，是在$mount进行的，会再去执行mount函数。







![study](C:\drawIo图片流程图\学习图片\study.png)





