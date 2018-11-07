#Vue.js(4)实列与内置组件学习笔记
---
##实例入门-实例属性
    <div id="app">
        {{message}}
    </div>
 
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
            //在Vue中使用jQuery
            mounted:function(){
                $('#app').html('我是jQuery!');
            }
        })
    </script>
jquery必须在mounted里才能操作  

##实例方法
一、$mount方法
$mount方法是用来挂载我们的扩展的，我们先来复习一下扩展的写法。

这里我们作了jspang的扩展，然后用$mount的方法把jspang挂载到DOM上，我们也生成了一个Vue的实例，直接看代码。
    <div id="app">
        {{message}}
    </div>
    
    <script type="text/javascript">
      var jspang = Vue.extend({
          template:`<p>{{message}}</p>`,
          data:function(){
              return {
                  message:'Hello ,I am JSPang'
              }
          }
      })
      var vm = new jspang().$mount("#app")
    </script>
即将扩展挂载到app的Dom上。


##实例事件
    <div id="app">
        {{num}}
    </div>
    <button onclick='reduce()'>reduce</button>
    <button onclick='off()'>off</button>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                num:1
            }
           
        })
        app.$on('reduce',function(){
            console.log('执行reduce()');
            this.num--;
        })
        function reduce(){
            app.$emit('reduce');
        }
        function off(){
      app.$off('reduce');
       }
    </script>
用on在构造器外部添加事件，之后用emit驱动它。   
off可以关闭事件

##内置组件 -slot讲解

    <div id="app">
    <jspang>
        <span slot="bolgUrl">{{jspangData.bolgUrl}}</span>    
        <span slot="netName">{{jspangData.netName}}</span>    
        <span slot="skill">{{jspangData.skill}}</span>    
    </jspang>
    </div>
 
	 
	<template id="tmp">
	    <div>
	        <p>博客地址：<slot name="bolgUrl"></slot></p>
	        <p>网名：<slot name="netName"></slot></p>
	        <p>技术类型：<slot name="skill"></slot></p>
	        
	    </div>
	</template>
 
    <script type="text/javascript">
        var jspang={
            template:'#tmp'
        }
 
        var app=new Vue({
            el:'#app',
            data:{
               jspangData:{
                   bolgUrl:'http://jspang.com',
                   netName:'技术胖',
                   skill:'Web前端'
               }
            },
            components:{
                "jspang":jspang
            }
        })
    </script>
即将自定义组件里面再插入<solt></solt>组件即  

	    <div>
	        <p>博客地址：<span slot="bolgUrl">{{jspangData.bolgUrl}}</span> </p>
	        <p>网名： <span slot="netName">{{jspangData.netName}}</span>    </p>
	        <p>技术类型： <span slot="skill">{{jspangData.skill}}</span></p>
	        
	    </div>

其中在html中solt属性传递，模板内<solt name=""></solt>接受