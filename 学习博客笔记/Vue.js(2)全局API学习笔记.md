#Vue.js(2)全局API学习笔记
----
##Vue.directive自定义指令

    <div id="app">
        <div v-jsfan='color'>颜色</div>
    </div>
 
    <script type="text/javascript">
        Vue.directive('jsfan',function(el,binding,vnode){
                el.style='color:'+binding.value;
        });
        var app=new Vue({
            el:'#app',
            data:{
               color:'red'  
            }
        })
    </script>
Vue.directive申请一个自定义指令，第一个参数是指令名字，第二个是函数。  
以我的理解el是当期的元素，binding是相当于一个v-jsfan的对象，value是获取color的值red

##Vue.extend构造器的延伸

    <author></author>
    <div id="author"></div>
    <script type="text/javascript">
    var authorExtend = Vue.extend({
            template:"<p><a :href='authorUrl'>{{authorName}}</a></p>",
            data:function(){
                return{
                    authorName:'JSPang',
                    authorUrl:'http://www.jspang.com'
                    }
                
            }
        });
        new authorExtend().$mount('author');
        new authorExtend().$mount('#author');
Vue.component用来生成组件，可以简单理解为当在模板中遇到该组件名称作为标签的自定义元素时，会自动调用“扩展实例构造器”来生产组件实例，并挂载到自定义元素上。  
，#挂载到id  .为挂载到类 直接author即挂载到标签<author></author>

##Vue.set全局操作
    <div id="app">
        <ul>
            <li v-for="aa of arr">{{aa}}</li>
        </ul>
    </div>
    <button onclick="add()">提交</button>



    <script type="text/javascript">
    function add(){
        Vue.set(outData.arr,1,'ddd');
    }
    var outData={
            arr:['aaa','bbb','ccc']
        }
        var app=new Vue({
            el:'#app',
            data:outData
        })
    </script>
常用来更新数组，因为vue更新下标是不会响应的,所以用Vue.set()

##Vue的生命周期（钩子函数）

    <div id="app">
        {{message}}
        <p><button @click="jia">加分</button></p>
    </div>
        <button onclick="app.$destroy()">销毁</button>
 
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:1
            },
            methods:{
                jia:function(){
                    this.message ++;
                }
            },
            beforeCreate:function(){
                console.log('1-beforeCreate 初始化之前');
            },
            created:function(){
                console.log('2-created 创建完成');
            },
            beforeMount:function(){
                console.log('3-beforeMount 挂载之前');
            },
            mounted:function(){
                console.log('4-mounted 挂载之后');
            },
            beforeUpdate:function(){
                console.log('5-beforeUpdate 数据更新前');
            },
            updated:function(){
                console.log('6-updated 被更新后');
            },
            activated:function(){
                console.log('7-activated');
            },
            deactivated:function(){
                console.log('8-deactivated');
            },
            beforeDestroy:function(){
                console.log('9-beforeDestroy 销毁之前');
            },
            destroyed:function(){
                console.log('10-destroyed 销毁之后')
            }
 
        })
    </script>

首先运行1，2，3，4  ，，点击加分运行5，6，，点击销毁运行9，10


##Template 制作模版

一、直接写在选项里的模板  

	 var app=new Vue({
	     el:'#app',
	     data:{
	         message:'hello Vue!'
	      },
	     template:`
	        <h1 style="color:red">我是选项模板</h1>
	     `
	})

二、写在<template>标签里的模板

    <template id="demo2">
          <h2 style="color:red">我是template标签模板</h2>
     </template>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
            template:'#demo2'
        })
    </script>


三、写在script标签里的模板  
这种写模板的方法，可以让模板文件从外部引入。

    <script type="x-template" id="demo3">
        <h2 style="color:red">我是script标签模板</h2>
    </script>
 
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
            template:'#demo3'
        })
    </script>

以上都是在app的div中直接放模板出来

##Component 初识组件  
一、全局化注册组件
    <div id="app">
        <jspang></jspang>
    </div>
 
    <script type="text/javascript">
        //注册全局组件
        Vue.component('jspang',{
            template:`<div style="color:red;">全局化注册的jspang标签</div>`
        })
        var app=new Vue({
            el:'#app',
            data:{
            }
        })
二、局部注册组件局部注册组件和全局注册组件是向对应的，局部注册的组件只能在组件注册的作用域里进行使用，其他作用域使用无效。
    <div id="app">
      <panda></panda>
    </div>
 
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            components:{
                "panda":{
                    template:`<div style="color:red;">局部注册的panda标签</div>`
                }
            }
        })
    </script>

##Component 组件props 属性设置
一、定义属性并获取属性值  
定义属性我们需要用props选项，加上数组形式的属性名称，例如：props:[‘here’]。在组件的模板里读出属性值只需要用插值的形式，例如{{ here }}.
    <div id="app">
      <panda here="China"></panda>
    </div>
 
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            components:{
                "panda":{
                    template:`<div style="color:red;">Panda from {{ here }}.</div>`,
                    props:['here']
                }
            }
        })
    </script>
这样就能获得here的属性值为China

三、在构造器里向组件中传值

	
     <panda < v-bind:here="message"></panda>

        var app=new Vue({
            el:'#app',
            data:{
               message:'SiChuan' 
            },
            components:{
                "panda":{
                    template:`<div style="color:red;">Panda from {{ here }}.</div>`,
                    props:['here']
                }
            }
        }) 
可以获取构造器中message的值SiChuan


##Component 父子组件关系
    <div id="app">
      <jspang></jspang>  
    </div>
    <script type="text/javascript">
       var city={
           template:`<div>Sichuan of China</div>`
       }
        var jspang = {
            template:`<div>
                    <p> Panda from China!</p>
                    <city></city>
            </div>`,
            components:{
                "city":city
            }
        }
        var app=new Vue({
            el:'#app',
            components:{
                "jspang":jspang
            }
           
        })
    </script>
其中city要放在jspang的上面，不然识别不到

##Component 标签

    <div id="app">
       <component v-bind:is="who"></component>
       <button @click="changeComponent">changeComponent</button>
    </div>
 
    <script type="text/javascript">
        var componentA={
            template:`<div style="color:red;">I'm componentA</div>`
        }
        var componentB={
            template:`<div style="color:green;">I'm componentB</div>`
        }
        var componentC={
            template:`<div style="color:pink;">I'm componentC</div>`
        }
       
        var app=new Vue({
            el:'#app',
            data:{
                who:'componentA'
            },
            components:{
                "componentA":componentA,
                "componentB":componentB,
                "componentC":componentC,
            },
            methods:{
                changeComponent:function(){
                    if(this.who=='componentA'){
                        this.who='componentB';
                    }else if(this.who=='componentB'){
                        this.who='componentC';
                    }else{
                        this.who='componentA';
                    }
                }
            }
        })
    </script>
通过点击按钮，来实现改变模板A，B，C。一定要是is属性才可以。
