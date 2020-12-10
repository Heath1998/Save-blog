# 源码文件解释



通过createApp进入，会去解析模板，通过effect去执行渲染。就像vue2是render渲染挂载到Watcher上一样。



isFlushing是异步在runtime-core/scheduler上

tigger在reactivity/effects上