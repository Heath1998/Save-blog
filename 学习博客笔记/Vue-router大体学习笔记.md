#Vue-router大体学习笔记
---
##一Vue-route安装
vue-router是一个插件包，所以我们还是需要用npm来进行安装的。  

	npm install vue-router --save-dev

router/index.js文件  
若要增加一个hi路由的页面，即网址地址为 http://localhost:8080/#/hi时可以显示新页面  
首先在component目录下新建一个hi.vue，以下为模板

	<template>
	  <div class="hello">
	    <h1>{{ msg }}</h1>
	  </div>
	</template>
	 
	<script>
	export default {
	  name: 'hi',
	  data () {
	    return {
	      msg: 'Hi, I am JSPang'
	    }
	  }
	}
	</script>
	 
	 
	<style scoped>
	 
	</style>

引入 hi组件：我们在router/index.js文件的上边引入Hi组件

	import Hi from '@/components/Hi'
增加路由配置：在router/index.js文件的routes[]数组中，新增加一个对象，代码如下。

	{
	  path:'/hi',
	  name:'hi',
	  component:hi
	}

router-link制作导航
 	
	<router-link to="/">[显示字段]</router-link>
/是首先，若要到达hi页面将 to改为='/hi'即可


##第二节vue-router配置子路由
现在我们想在hi下配置两个子路由hi1和hi2，具体实现如下：

首先在components/Hi.vue下加入路由
  
	<router-view ></router-view>

在components目录下新建两个组件模板 Hi1.vue 和 Hi2.vue  
修改router/index.js代码

	{
	      path:'/hi',
	      component:Hi,
	      children:[
	        {path:'/',component:Hi},
	        {path:'hi1',component:Hi1},
	        {path:'hi2',component:Hi2},
	      ]
	    }

这样就可以进入hi/hi1和hi/hi2了。

##第三节单页面多路由区域操作

	<router-view ></router-view>
	 <router-view name="left" style="float:left;width:50%;background-color:#ccc;height:300px;"></router-view>
	 <router-view name="right" style="float:right;width:50%;background-color:#c0c;height:300px;"></router-view>

之后再配置路由

    {
      path: '/',
      components: {
        default:Hello,
        left:Hi1,
        right:Hi2
      }
    },{
      path: '/Hi',
      components: {
        default:Hello,
        left:Hi2,
        right:Hi1
      }
    }
这样就实现/页面下和/Hi页面下左右路由的模板component是不同的

##第四节vue-router 利用url传递参数
**:冒号的形式传递参数**  
在配置文件里以冒号的形式设置参数。我们在/src/router/index.js文件里配置路由。

	{
	    path:'/ax/:newsId/:newsTitle',
	     component:Params
	}
之后在component下建立Params.vue模板  
之后输入网址/params/198/ everything is very good时就可以在Params.vue模板下接受

        <p>新闻ID：{{ $route.params.newsId}}</p>
        <p>新闻标题：{{ $route.params.newsTitle}}</p>
这样直接就可以获取url的参数传递很简单，例如/ax/234/news 可以传递置Params.vue模板  
$route.params.+绑定的数据名称，则可以获得传递数据。


##第五节vue-router 的重定向-redirect
开发中有时候我们虽然设置的路径不一致，但是我们希望跳转到同一个页面，或者说是打开同一个组件。这时候我们就用到了路由的重新定向redirect参数。  
**redirect基本重定向**

	export default new Router({
	  routes: [
	    {
	      path: '/',
	      component: Hello
	    },{
	      path:'/params/:newsId(\\d+)/:newsTitle',
	      component:Params
	    },{
	      path:'/goback',
	      redirect:'/'
	    }
	 
	  ]
	})

**重定向时传递参数**

	{
	  path:'/params/:newsId(\\d+)/:newsTitle',
	  component:Params
	},{
	  path:'/goParams/:newsId(\\d+)/:newsTitle',
	  redirect:'/params/:newsId(\\d+)/:newsTitle'
	}
已经有了一个params路由配置，我们在设置一个goParams的路由重定向，并传递了参数。这时候我们的路由参数就可以传递给params.vue组件了。

##第六节alias别名的使用

	{
	    path: '/hi1',
	    component: Hi1,
	    alias:'/other'
	 }
这样输入网址/other就和hi1页面相同

redirect和alias的区别  
redirect：仔细观察URL，redirect是直接改变了url的值，把url变成了真实的path路径。  
alias：URL路径没有别改变，这种情况更友好，让用户知道自己访问的路径，只是改变了<router-view>中的内容  


别名请不要用在path为’/’中，如下代码的别名是不起作用的。

	{
	  path: '/',
	  component: Hello,
	  alias:'/home'
	}

##第八节路由的过渡动画

	<transition>标签  
想让路由有过渡动画，需要在<router-view>标签的外部添加<transition>标签，标签还需要一个name属性。  
css过渡类名：
组件过渡过程中，会有四个CSS类名进行切换，这四个类名与transition的name属性有关，比如name=”fade”,会有如下四个CSS类名：  

fade-enter:进入过渡的开始状态，元素被插入时生效，只应用一帧后立刻删除。   
fade-enter-active:进入过渡的结束状态，元素被插入时就生效，在过渡过程完成后移除。  
fade-leave:离开过渡的开始状态，元素被删除时触发，只应用一帧后立刻删除。  
fade-leave-active:离开过渡的结束状态，元素被删除时生效，离开过渡完成后被删除。  
	
	.fade-enter {
	  opacity:0;
	}
	.fade-leave{
	  opacity:1;
	}
	.fade-enter-active{
	  transition:opacity .5s;
	}
	.fade-leave-active{
	  opacity:0;
	  transition:opacity .5s;
	}
in-out:新元素先进入过渡，完成之后当前元素过渡离开。  
out-in:当前元素先进行过渡离开，离开完成后新元素过渡进入。  
在transition上加入mode='in-out'即可

##第九节mode的设置和404页面的处理
在roter的index.js文件下mode:''  
mode的两个值  
histroy:当你使用 history 模式时，URL 就像正常的 url，例如 http://jsapng.com/lms/，也好看！  
hash:默认’hash’值，但是hash看起来就像无意义的字符排列，不太好看也不符合我们一般的网址浏览习惯。   

**404页面的设置：**  

	{
	   path:'*',
	   component:Error
	}
再创建一个Error的component即可。  

##第十节杂项
一些钩子函数的运用。  
this.$router.go(-1);后退操作  
this.$router.go(1);前进操作  
