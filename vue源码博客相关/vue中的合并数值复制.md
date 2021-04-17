#   vue中的合并数值复制

```javascript
  Object.defineProperty(Vue.prototype, '$router', {
    get () { return this._routerRoot._router }
  })
```

向上方那样写的话，每一个组件实例都可以访问$router属性。 

具体原因是



```javascript
	// this为Vue构造函数
	const Super = this
    
    const Sub = function VueComponent (options) {
      this._init(options)
    }
    Sub.prototype = Object.create(Super.prototype)
    Sub.prototype.constructor = Sub
    Sub.cid = cid++
    Sub.options = mergeOptions(
      Super.options,
      extendOptions
    )
    Sub['super'] = Super

因为 Object.create(Super.prototype)用原型去继承，则每一个vue组件实例都访问的到$router,会一层一层往上找。
```



