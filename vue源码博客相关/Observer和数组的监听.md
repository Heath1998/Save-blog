# __ob__的作用

判断当前observe(value)时，value对象是否已存在__ob__的Observere对象，若存在直接返回Observer对象。简单来说就是向对象中加入属性__ob__, value值为Observer对象。

这样的话当指向对象一样就不会再去双向绑定多一次，而是直接用。


# 关于数组的监听

先获取数组的prototype原型对象，之后新建一个对象arrayMethods，之后遍历7个变态方法。   
在arrayMethods上增加push等方法
用def绑定每一个变态方法，方法是执行原型数组的方法用apply()在this下执行，this就是调用这个方法的数组。先获取当前this的__ob__,这个就是数组的Observer对象给ob。   
之后如果是push，unshift，splice就可能增加数组元素，执行ob.observerArray(inserted)，就是observe(inserted),来看inserted中的每一个元素是不是对象是的话深度遍历。

之后调用ob.dep.notify()通知变动，更新视图。


获得arrayMethods对象后，在new Observer（val）如果当前val是数组的话，会将当前val数组的__proto__指向arrayMethods对象，这样调用push方法就是调用arrayMethods上的方法。