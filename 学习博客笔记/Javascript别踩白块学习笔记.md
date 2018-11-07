#别踩白块学习前端Javascript练习笔记
----
![图片](https://i.imgur.com/CaPs4gl.png)

总体思路，首先HTML  
是一个外部div id='main'，内部一个div id='con'  

其次是css将main放置居中，之后创造row为一排，cell正方块，black黑块。  

最后是Javascript的运用：  row的移动函数move()，增加新的一排row的函数createrow()  
删除最后一排row的函数delrow()， 记分函数score(),点击绑定函数judge();  
之后就是top=-100px,运行init，每30ms运行一次move(),top下降speed的px，到底时判断是否失败结束
否则进行相关操作。



    function creatediv(className) {  
    var div=document.createElement('div');  
    div.className=className;  
    return div;  
    }
    //创建一个div并把类名className赋给div.className

---
     function createcell() {
       var temp=["cell","cell","cell","cell"];
       var i = Math.floor(Math.random()*4);
       temp[i]="cell black";
       return temp;
     }
     //创建一个字符串数组并随机生成一个黑块，并返回这个字符串数组

---
       function judge(ev) {
       if(ev.target.className.indexOf('black')!=-1)
       {
           ev.target.className='cell';
           ev.target.parentNode.pass=1;
           score();
       }
    }
    //绑定了点击事件，这个ev。target会是黑色方块，ev.target返回触发这个事件的元素
    //className为类名，indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置。
    //增加pass属性后面判断是否结束

---
      function move() {
        var con=$('con');
        var top=parseInt(window.getComputedStyle(con,null)['top']);
        if(top+speed>0)
            top=0;
        else
            top+=speed;
        con.style.top=top+"px";

        if(top==0)
        {
            createrow();
            con.style.top="-100px";
            delrow()
        }
        else if(top==(-100+speed))
        {
            var rows=con.childNodes;
            if((rows.length==5)&&(rows[rows.length-1].pass!=1))
                fail();
        }
    }
    //获取con即大正方块window.getComputedStyle(con,null)['top']获取con元素的top值
    //con.childNodes获取con元素的子元素数组
    //正常游戏一直都是5个row，到六个就要删除一个，最后length=5且最后一个pass！=1即黑色没被消除

---
    function init() {
       $('main').onclick=function (ev) {
           judge(ev);
       }
       // 定时器 每30毫秒调用一次move()
       clock = window.setInterval('move()', 30);
    }
    //初始化js绑定事件到judge，之后每隔30ms调用一次move()
     function fail() {
     clearInterval(clock);
     confirm('你的最终得分为 ' + parseInt($('score').innerHTML) );
    }




----
##总结学习到的JavaScript函数有
	**var div=document.createElement('div')  //为创造div元素**  
	**con.appendChild(row);    //con为父元素，row为要插入的子元素，插入到con最后面**  
	**con.insertBefore(row,con.firstChild);  //插入置某元素之前，row插入置con父元素的firstChild第一个元素之前**  
	**con.removeChild(con.lastChild); //移除一个子元数**  
	**ev.target.//返回触发这个事件的元素**  
	**window.getComputedStyle(con,null)['top'] //获取con元素的top值**  
	**clock = window.setInterval('move()', 30); //每隔30ms执行一次move()**
	**clearInterval(clock); //停止30ms执行一次**