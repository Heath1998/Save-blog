##学习vue框架一个简单MVVM框架的学习笔记---记录仅为个人学习

参考资料：  
[理解vue原理，实现简单vue框架](https://blog.csdn.net/pur_e/article/details/53066275 )  
[解析vue原理，如何实现双向绑定](https://github.com/DMQ/mvvm)  
[vue双向绑定原理](https://www.cnblogs.com/kidney/p/6052935.html)

##一设计思路
因为我是在看了这几篇文章才稍微理解了vue的双向绑定，所以我这里有一点上帝视角的意思。就好像应当这样的。  
如果要实现一个简单的双向绑定的MVVM框架至少要以下几点：  
 1.首先要会利用Object.defineProperty，将data对象里的数据绑定上getter/setter，以便拦截对数据的访问和设置，称之为Observer（观察者）；  
 2.需要解析dom，解析出相关指令和文本，并对它进行相关操作，称之Compiler 。  
 3.需要一个用以监听数据变化的监听队列和发生改变时发布消息函数的Dep简称为发布者Dep。   
 4.有了侦听队列就要有一个监听器，进行接受dom的变化，并执行相关的更新dom，称之为Watcher  
 5.最后需要一个可以公共入口，协调以上步骤，称之为vue  

简单说一下总体步骤，  

	<div id='app'>
		<p v-text='textStr'></p>
	</div>
	
	var vm = new MVVM({
		el:'#app'，
		data:{
		textStr:'this is a text'
		}
	})
 
首先   
1.MVVM接口传入对象options，之后MVVM进行相关的数据绑定，赋值和代理。  
2.执行Observer(this.$data)来进行第一大步数据绑定上getter/setter    
3.绑定后执行compiler进行dom的解析，最后执行new Watch（）建立一个新的监听器。  
4.执行Watcher过程中会将此侦听器加入侦听队列Dep中，创建Watcher过程中会执行一次update更新函数，更新dom中的数据。  
5.之后每次改变数据会调用setter来执行Dep的notify来更新数据。  

##二、实现Observer
这里就简单的说一下过程方便我本人再回忆  
1.遍历object中的每一个key值，Object.defineProperty绑定getter/setter，之后要记得遍历子节点即observe（val）。  
2.监视对象时记得var dep = new Dep（）并在getter中Dep.target && dep.addSub(Dep.target);将watcher侦听器加入dep中， 之后在setter中记得执行dep.notify()来执行dom中的数据数据更新

##三，实现compiler
简单陈述一下：用遍历得到文档碎片返回fragment，之后this.compile(this.$fragment)，再  this.$el.appendChild(this.$fragment)。其中compile函数是主要解析dom的，当然要记得循环遍历每一个子节点。当然以nodeType进入不同的函数，之后执行不同的处理例如：modelHandler，textHandler，htmlHandler。里面再绑定监听者bindWatcher（）里面有更新数据函数。

##四，实现watcher
主要功能就是监听数据变化并执行update数据更新。   
Watcher作用域中get函数获取value值并把此监听器加入监听队列。  
update函数作用就是获取新的newVal，执行compiler传入的回调函数进行数据更新。  

##五，实现Dep发布者
主要功能是将侦听器挂载到上面并在要执行发布消息notify时调用watcher的更新函数。  

##六，实现Vue的接口和创建
第一点

    options = Object.assign({},
        {
            computed: {},
            methods : {}
        },
        options);

data和computed代理到vue对象上，而methods方法直接挂载到vue上不用代理。  
var ob = new Observer(this.$data);给this.$data绑定setter/getter。   
new Compiler({el: this.$el, vm: this});解析dom结构并在其中挂载相应的侦听器。  

##七，父子组件部分
即component模块，过了好久才补充的，我就简单讲一下父向子传模块吧。  
基本思想就是解析dom元素时检查标签是否与components的标签一样，若一样就按照子组件的template创建dom节点之后，相当于再new 一个MVVM，之后再把这个dom节点插入原来父节点的地方，当然里面有许多复杂的操作，这里详细讲。

##八，遇到的一些需要注意的问题
观察者中Observer  
1，observeObject方法要绑定时，记得如果是对象要遍历子节点即this.observe(val)。  
2，还有监控数组，但这一点有点难。

Compiler  
1，编译元素节点时，要记得在结尾遍历子元素节点。  
过程基本为1->执行nodeToFragment获取元素文档碎片    
2->执行compile（）编译dom，compile根据节点是元素还是文档来进行分类。之后根据v-XXX指令来解析跳到XXXHandler处理函数，其中再执行bindWatcher（）来新建绑定侦听器。最后有一个updater对象，里面有各种实现更新函数，创建Watcher将更新函数传入。   
3-> this.$el.appendChild(this.$fragment)将文档碎片插入dom中

Watcher  
1.其中Watcher作用域中有两个函数get()是用来将侦听器加入侦听队列，并获取相应的数据val。 
update()用来执行get（），并更新dom执行compiler传进来的更新数据函数。  

Dep  
1.两个函数addSub()增加监听器， notify()执行所有监听器的更新函数。   



