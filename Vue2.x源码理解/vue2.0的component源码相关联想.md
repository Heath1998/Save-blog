# vue2.0的component源码相关联想



## 关于render



```javascript
function() {
  with(this){return _c('div',[_c('test')],1)}
}
```

执行生成一个vnode对象。一般第一个是id=“app”的div标签



- `Vue`根实例初始化会执行 `vm.$mount(vm.$options.el)`实例挂载的过程，按照之前的逻辑，完整流程会经历`render`函数生成`Vnode`,以及`Vnode`生成真实`DOM`的过程。



```javascript
  Vue.prototype._update = function (oldVnode, newVnode, vm) {
    patch(oldVnode, newVnode, vm)
  }
  Vue.prototype.$mount = function (el) {
    const vm = this;
    const ele = document.querySelector(el).outerHTML.trim();
    const ast = translateToAst(ele);
    const render = translateTorender(ast);
    const _render = function () {
      return render.call(vm)
    }
    vm.$options._render = _render;
    const fn = function () {
      // 获取render并执行生成vnode虚拟dom节点对象
      let vnode = vm.$options._render();
      console.log(vnode)
      // 执行更新patch
      vm._update(vm.vnode, vnode, vm);
      vm.vnode = vnode
    }
    // 用watch监听改变
    new Watcher(fn, this, false)
  }
```



根据我简单的理解  

数据变化，执行.__update会先执行render()获取vnode虚拟节点，再通过patch(oldVnode,vnode)去对比更新节点elm



  

若使用主vue包含一个子组件。 当一开始时会执行一次render，会render编译出虚拟的dom，即vnode

当执行到组件化标签时。如<myComponent/>时，会去构建一个子组建Vnode，名称为自组件名称。     

若局部组件的话，就在构建组件vnode时去注册子类构造器。子类构造器，相当于继承Vue的原型的类。    



当执行patch(oldvnode,vnode)更新时，由于oldvnode为空，会直接createElm出事化，这个过程会新建每一个非组件标签并且insert加入。若到子组件标签时，会new 子类构造器出一个实例。这个实例也会初始化，执行相应的vue初始化，也会执行render，patch这些。构建所有标签并挂载，这个时候再将elm元素加入子组建的vnode上，并且将elm执行insert插入展现。



### vnode创建流程

![image-20201028103351538](C:\Users\80302152\AppData\Roaming\Typora\typora-user-images\image-20201028103351538.png)





### 真实节点dom渲染





![image-20201028103317902](C:\Users\80302152\AppData\Roaming\Typora\typora-user-images\image-20201028103317902.png)











当主组件的某些数据变化重新，会更新视图会在patchvnode中更新子的props等内容。      

当子组件自己的数据更新时只会触发子组建的update，只会更新挂载在id=“子组建”的内容，父组件还是在使用当前元素标签。





```javascript
  var i;
  var data = vnode.data;
// 传递props等
if (isDef(data) && isDef((i = data.hook)) && isDef((i = i.prepatch))) {
    i(oldVnode, vnode);
  }

  var oldCh = oldVnode.children;
  var ch = vnode.children;

  if (isDef(data) && isPatchable(vnode)) {
    for (i = 0; i < cbs.update.length; ++i) {
      cbs.update[i](oldVnode, vnode);
    }

    if (isDef((i = data.hook)) && isDef((i = i.update))) {
      i(oldVnode, vnode);
    }
  }

```



