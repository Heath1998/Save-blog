## Vue的extend源码学习

### extend主要作用

```javascript
Vue.extend = function (extendOptions) {
  const Super = this
  const Sub = function VueComponent (options) {
      this._init(options)
  }
  Sub.prototype = Object.create(Super.prototype)
  Sub.prototype.constructor = Sub
  Sub.options = mergeOptions(Super.options, extendOptions)
  Sub['super'] = Super
  if (Sub.options.props) {
      initProps(Sub)
  }
  if (Sub.options.computed) {
      initComputed(Sub)
  }  
}
```



大概做了以上代码，extend主要返回一个继承Vue的子类，它会合并修改子类的options。之后判断执行initProps和initComputed



```javascript
Vue.options  内容为
{
	components: {keepalive.....,transition....,和你注册的全局组件},
	directives: {包含指令},
	filter: {你定义的过滤器},
	_base:只有合并过的选项会带有_base属性
}
```





其实就是将传入的组件options转换为一个子构造函数，通过mergeOptions，生成Vue的子类，



