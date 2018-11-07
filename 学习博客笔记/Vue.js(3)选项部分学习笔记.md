#Vue.js(3)选项部分学习笔记
---
##propsData Option  全局扩展的数据传递
    <header></header>
 
    <script type="text/javascript">
        var header_a=Vue.extend({
            template:`<p>{{message}}--{{a}}</p>`,
            data:function(){
                return{
                    message:'Hello,propsData'
                }
            },
            props:['a']
        });
        new header_a({propsData:{a:'two'}}).$mount('header');
首先建立一个全局扩展做一个自定义标签，之后用mount进行挂载，  
并用propsData:{key:'data'}来进行参数传递，用props:['key']来进行接受。

##computed Option  计算选项


	computed:{
	    reverseNews:function(){
	        return this.newsList.reverse();
	    }
	}
将数组newsList进行反转。

##Methods Option  方法选项
    <div id="app">
        {{message}}<br>
        <button v-on:click='add(2,$event)'>add</button><br>
        <bln v-on:click.native='add(2)'></bln>
    </div>
    <button onclick="app.add(2)">外部add</button>
    <script type="text/javascript">
    var bln={
        template:`<button>内部按钮</button>`
    }
        var app=new Vue({
            el:'#app',
            data:{
                message:1
            },
            methods:{
                add:function(num){
                    this.message+=num;
                    console.log(event);
                }
            },
            components:{
                'bln':bln
            }
        })
    </script>

native  给组件绑定构造器里的原生事件。构造器中的模板bln必须要用.native原生才能使用  
作用域外部调用构造器里的方法，即指app.add(2)  //不经常使用

##Watch 选项 监控数据

     var suggestion=['T恤短袖','夹克长裙','棉衣羽绒服'];
        var app=new Vue({
            el:'#app',
            data:{
                temperature:14,
                suggestion:'夹克长裙'
            },
            methods:{
                add:function(){
                    this.temperature+=5;
                },
                reduce:function(){
                    this.temperature-=5;
                }
            },
            watch:{
                temperature:function(newVal,oldVal){
                    if(newVal>=26){
                        this.suggestion=suggestion[0];
                    }else if(newVal<26 && newVal >=0)
                    {
                        this.suggestion=suggestion[1];
                    }else{
                        this.suggestion=suggestion[2];
                    }
                }
            }

        })
用watch可以监控数据有无改变。  

用实例属性写watch监控  
有些时候我们会用实例属性的形式来写watch监控。也就是把我们watch卸载构造器的外部，这样的好处就是降低我们程序的耦合度，使程序变的灵活。

	app.$watch('temperature',function(newVal,oldVal){
	    if(newVal>=26){
	        this.suggestion=suggestion[0];
	    }else if(newVal<26 && newVal >=0)
	    {
	        this.suggestion=suggestion[1];
	    }else{
	        this.suggestion=suggestion[2];
	    }
	 
	})
这样写和上面写是一样的。

##Mixins 混入选项操作
一、Mixins的基本用法

    <div id="app">
        <p>num:{{ num }}</p>
        <P><button @click="add">增加数量</button></P>
    </div>
 
    <script type="text/javascript">
        //额外临时加入时，用于显示日志
        var addLog={
            updated:function(){
                console.log("数据放生变化,变化成"+this.num+".");
            }
        }
        var app=new Vue({
            el:'#app',
            data:{
                num:1
            },
            methods:{
                add:function(){
                    this.num++;
                }
            },
            mixins:[addLog]//混入
        })
    </script>
这样就混入了构造器了，每当数据更新就执行（钩子函数）。

二、mixins的调用顺序  
从执行的先后顺序来说，都是混入的先执行，然后构造器里的再执行，需要注意的是，这并不是方法的覆盖，而是被执行了两边。  

三、全局API混入方式
我们也可以定义全局的混入，这样在需要这段代码的地方直接引入js，就可以拥有这个功能了。我们来看一下全局混入的方法：

	
	Vue.mixin({
	    updated:function(){
	        console.log('我是全局被混入的');
	    }
	})
PS：全局混入的执行顺序要前于混入和构造器里的方法。

##Extends Option  扩展选项
    <div id="app">
        {{message}}<br>
        <button @click='add'>add</button>
    </div>
 
    <script type="text/javascript">
    var extendouter={
        updated:function(){
            console.log('我是外部的方法');
        }
    };
        var app=new Vue({
            el:'#app',
            data:{
                message:1
            },
            methods:{
                add:function(){
                    this.message++;
                    console.log('我是原生的方法')
                }
            },
            extends:extendouter
        });
    </script>
也可以添加到构造器中