#JavaScript
-----
##内置对象
1.数学对象  
   随机生成n-m的一个整数  
Math.random()函数返回0和1之间的伪随机数，可能为0，但总是小于1，[0,1)  
　　第一步算出 m-n的值，假设等于w

　　第二步Math.random()*w

　　第三步Math.random()*w+n

　　第四步Math.round(Math.random()*w+n)或者 Math.ceil(Math.random()*w+n)  
  
2.日期对象  
var date=new Date();获得一个时间戳  
date.toString();获得一个中国标准时间的字符串
myDate.getFullYear();    //获取完整的年份(4位,1970-????)  
myDate.getMonth();       //获取当前月份(0-11,0代表1月)  
myDate.getDate();        //获取当前日(1-31)  
myDate.getHours();       //获取当前小时数(0-23)  
myDate.getMinutes();     //获取当前分钟数(0-59)  
myDate.getSeconds();     //获取当前秒数(0-59)  

##计时器的四个方法  
1.setInterval("fun()","1000");每过1秒调用一次fun()  
2.clearInterval(对象); 清除已设置的setInterval对象  
3.setTimeout(Expression,DelayTime),在DelayTime过后,将执行一次Expression,setTimeout 运用在延迟一段时间，再进行某项操作。  
4.1clearTimeout(对象) 清除已设置的setTimeout对象

##Dom的增加，删除节点
1.增加节点  

	<div id="div1">  
	<p id="p1">这是一个段落/p>  
	<p id="p2">这是另一个段落/p>  
	</p>  


	var para=document.createElement("p");  
	para.innerHTML="这是增加的标签文本";  
	var element=document.getElementById("div1");  
	element.appendChild(para);  

2.删除节点  

	<div id="div1">  
	<p id="p1">这是一个段落/p>  
	<p id="p2">这是另一个段落/p>  
	</p>  

	var parent=document.getElementById("div1");  
	var child=document.getElementById("p1");  
	parent.removeChild(child);

3.querySelector()的使用  
获取文档中 id="demo" 的元素： 

	document.querySelector("#demo");

获取文档中第一个 p> 元素:

	document.querySelector("p");  

获取文档中 class="example" 的第一个元素:

	document.querySelector(".example");

获取文档中有 "target" 属性的第一个 a> 元素

	document.querySelector("a[target]");

4.querySelectorAll的使用  

	<div id="all" class="all">  
	<i>梦龙小站</i>  
	<p class="box">  
	<em class="span">梦龙小站/em>  
	</p>  
	<i class="span">梦龙小站/i>  
	<p class="box">  
	<em>梦龙小/em>  
	</p>  
	</div>  

//获取类名为all的div>中所有的<i>，元素类似于 

	 getElementsByTagName("i")  
	var i = document.getElementById("all").querySelectorAll("i");  
  
//获取类名为span的所有元素  

	var span = document.querySelectorAll(".span");  
  
//获取所有p>标签中的所有em>元素  

	var em = document.querySelectorAll("p em");  

  
	var i, len, emOne;  
	for(i=0, len = em.length; i<len; i++){  
	    emOne = em[i];  
	    //或者 em.item(i);  
	    emOne.className = "meng";  
	}  







##目前的javascrip的杂项
1.在新窗口打开新网页

	window.open(url,"_blank");  
*在原窗口跳转网页

	window.open(url,"_self");  
2.document.getElementById("abc").innerHTML="要修改的结果"选择器选择id为abc的对象并修改它的文本。  
3.document.getElementById("main-picture").getAttribute("src")返回一个元素为"main-picture"的对象的src属性的值。  
4.document.getElementById("imag2").setAttribute("class","active");设置id="imag2"的对象的属性class的值。  
