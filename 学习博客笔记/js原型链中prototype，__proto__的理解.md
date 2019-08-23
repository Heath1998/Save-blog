# js原型链中prototype，`__proto__`的理解（18年12.25）

今天开始认真重新过一遍js最基础的东西。话不多说，开始干货。

1，每一个构造函数都有一个属性prototype，它是一个指针，指向原型对象。   
2，每一个实例对象都有一个 `__oproto__ `的属性，同样，它也是一个指针，它也指向原型对象。  
3，每一个原型对象都有一个constructor属性，它是一个指针，它指向它的构造函数。

接下来讲一下js中的继承，因为es5没有类的概念。  
所以用原型继承来实现继承。直接上代码吧。  

	function father() {
	    this.num = "123";
	    this.fn = function () {
	        console.log(this.num);
	    }
	}
	
	father.prototype.str = "abc";
	
	function child() {
	    
	}
	
	child.prototype = new father();
	var childObj = new child();
	console.log(childObj.str);
	childObj.fn(); 

可以知道改变了子构造函数的原型指针，使之指向新new的父对象。   
这样新new的父对象就是子构造函数的原型对象。   
当新new的子对象访问属性时，先查找自己实例的this是否有无，若没有就在原型对象中找，   
原型对象也是同理，先找自己的this实例再再找原型对象里有没有，这样就实现了继承。

简单的来说是以下的图
![](http://myqianlan.qiniudn.com/blog/2015/04/13/proto.jpg)

任何原型对象都是继承的终点都是object。



