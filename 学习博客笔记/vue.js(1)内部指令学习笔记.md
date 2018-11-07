#vue.js(1)内部指令学习笔记
---
	
npm n install live-server -g

-----
    <div id="app">
        {{message}}
    </div>

  	var app=new Vue({
    el:'#app',
    data:{
    message:'hello Vue!'
    }
    })
    //此为vue.js的基本模板

##v-if&v-else&v-show
    <div id="app">
        <div v-show='disappear'>设置display，是否隐藏</div>
        <div v-if='islogin'>登陆成功</div>
        <div v-else='islogin'>请登录</div>
    </div>
v-if为true则执行登陆成功，为false显示请登录  
v-show为true，就设置display，v-show为false就设置隐藏  

##v-for
        <ul>
            <li v-for='item of sortitem'>
                {{item}}
            </li>
        </ul>
v-for将显示sortitem.length个<li></li>

##v-text
    <div id="app">
        <span v-text="message"></span><br/>
        <span v-html="msgHtml"></span>
    </div>
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!',
                msgHtml:'<h2>hello Vue!</h2>'
            }
        })
v-text就是要显示的纯文本  
v-html是要显示的html标签

##v-on:click
       本场比赛得分： {{count}}<br/>
       <button v-on:click="jiafen">加分</button>
       <button v-on:click="jianfen">减分</button><br>
            methods:{
                jiafen:function(){
                    this.count++;
                },
                jianfen:function(){
                    this.count--;
                }
			}

v-on绑定事件，函数卸载methods中

##v-model
        <p>v-model  <input type="text" v-model='message'></p>
        <p>v-model.lazy  <input type="text" v-model.lazy='message'></p>
        <p>v-model.number  <input type="text" v-model.number='message'></p>

v-model双向绑定，input输入框会改变message值，  
v-model.lazy省略空格，多个空格识别成一个  
v-model.number先输入数字就只能识别数字

##v-bind
    <div id="app">
        <img v-bind:src='imgsrc' width="200px"><br>
        <a :href='url'>jishupang</a><br>
        <div :class="className">1、绑定classA</div>
        <div :class='{classA:isOk}'>2,绑定class的判断</div>
        <div :class='[classA,classB]'>3,绑定class的数组</div>
        <div :class='isOk?classA:classB'>4,绑定class的3元判断</div>
        <hr>
        <div style="color:red;font-size:150%;">5、绑定style</div>
        <div :style="styleObject">6、用对象绑定style样式</div>
    </div>
        var app=new Vue({
            el:'#app',
            data:{
                imgsrc:'http://jspang.com/wp-content/uploads/2018/02/bbbb.jpg',
                url:'http://jspang.com/category/learn/vue2/',
                className:'classA',
                classA:'classA',
                classB:'classB',
                isOk:false,
                styleObject:{
                fontSize:'24px',
                color:'green'
            }
            }
        })
动态绑定src的值，v-bind:src,缩写:src  
<div :class='[classA,classB]'>3,绑定class的数组</div>绑定两个类值
<div :style="styleObject">6、用对象绑定style样式</div>绑定style的对象

##其他指令
    [v-cloak] {
    display: none;
    }
    </style>
    <div id="app">
        <div v-pre>{{message}}</div>
        <div v-cloak>
            {{ message }}
          </div>
          <div v-once>{{message}}</div>
          <input type="text" v-model='message'>
          <div>{{message}}</div>
    </div>

<div v-pre>{{message}}</div>  
这时并不会输出我们的message值，而是直接在网页中显示{{message}}  
  
v-cloak指令
在vue渲染完指定的整个DOM后才进行显示。它必须和CSS样式一起使用.

v-once指令
在第一次DOM时进行渲染，渲染完成后视为静态内容，跳出以后的渲染过程。
