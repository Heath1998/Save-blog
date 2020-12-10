## Vue的directive源码大体操作

###  Vue.directive的全局指令

```javascript
var ASSET_TYPES = ["component", "directive", "filter"];

ASSET_TYPES.forEach(function (type) {
  Vue[type] = function (id, definition) {
    if (!definition) {
      return this.options[type + "s"][id];
    } else {
      /* istanbul ignore if */
      if (process.env.NODE_ENV !== "production" && type === "component") {
        validateComponentName(id);
      }
      if (type === "component" && isPlainObject(definition)) {
        definition.name = definition.name || id;
        definition = this.options._base.extend(definition);
      }
      if (type === "directive" && typeof definition === "function") {
        definition = { bind: definition, update: definition };
      }
      this.options[type + "s"][id] = definition;
      return definition;
    }
  };
});
```

主要向Vue构造函数中的options添加了新指令命令。



###  添加局部组件

```javascript
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```

在执行mergeOption时会将当前options上的directives合并。

```javascript
/**
 * 合并组件选项
 */
function mergeOptions (
  parent,
  child,
  vm
) {
    ...
    normalizeDirectives(child);// 格式化指令
    ...
  if (!child._base) {
    if (child.extends) {
      parent = mergeOptions(parent, child.extends, vm);
    }
    if (child.mixins) {
      for (var i = 0, l = child.mixins.length; i < l; i++) {
        parent = mergeOptions(parent, child.mixins[i], vm);
      }
    }
  }

  var options = {};
  var key;
  for (key in parent) {
    mergeField(key);// 合并选项
  }
  for (key in child) {
    if (!hasOwn(parent, key)) {
      mergeField(key);// 合并选项
    }
  }
  function mergeField (key) {
    var strat = strats[key] || defaultStrat;// 获取合并策略
    options[key] = strat(parent[key], child[key], vm, key);
  }
  return options
}
```

这里会用叠加型去添加directives指令，就是通过将父级作为原型创建空对象，子级元素再分别拷贝至空对象。



### 标签指令解析

```html
<div v-test:name.fale="getName"></div>
```

解析成的虚拟dom，vnode为

```javascript
{
   .......
  directives: [
    {
      name: "test",
      rawName: "v-test:name.fale",
      value: getName,
      expression: "getName", // 表达式
      arg: "name", // 参数
      modifiers: { fale: true }, // 修饰符
    },
  ];
}
```

当节点渲染的过程中会有各种钩子函数，判断是否有当前指令，进而执行当前指令的钩子函数。





