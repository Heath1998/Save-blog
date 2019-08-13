# initWatch

异步渲染的重要点？

去重是为只执行一次更新，用到has[id]当有了，就会赋值has[id]为true，那下一次的就直接跳过。

排序：当queue执行时会按照watcherId从小到大排序，因为initState要按顺序执行

当执行回掉调函数时，执行队列中的watcher时的情况。 
他会从当前执行的位置开始从小到大排序，其实就是插入适当位置

vue这种回调函数运行中时会不断的加入新触发的watcher，去按顺序执行。

优点：
①直到这个队列执行完，都不会再调用nextTick去设置异步事件。
②运行中的watcher不用等到下一次异步任务，可以直接在本次queue数组中执行



cleanupDeps清除依赖收集，

this.cleanupDeps()
作用就是去掉当前watch中老的依赖的dep中subs的watcher自己。
这样触发老的依赖dep就不会notify通知自己。



当数据改变时会重新依赖收集，收集新的依赖
最后将newDeps赋值给deps，并将newDeps变为空
当新的依赖dep要notify就可以通知watcher本身了。
