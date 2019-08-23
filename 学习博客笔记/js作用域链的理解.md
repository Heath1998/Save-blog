(18.12.26)
# js作用域链的理解

## 一，执行环境
执行环境定义了变量和函数有权访问的其他数据。每个执行环境都有与之对应的变量对象（variable object），保存着该环境中定义的所有变量和函数。

## 1.1全局环境
全局执行环境是最外围的一个执行环境，在web浏览器中，我们可以认为他是window对象，因此所有的全局变量和函数都是作为window对象的属性和方法创建的。代码载入浏览器时，全局环境被创建，关闭网页或者关闭浏览时全局环境被销毁。

## 1.2函数执行环境
每个函数都有自己的执行环境，当执行流进入一个函数时，函数的环境就被推入一个环境栈中，当函数执行完毕后，栈将其环境弹出，把控制权返回给之前的执行环境。


## 二，作用域与作用域链

## 2.1全局作用域和局部作用域

	var name1 = "Tom";
	function fn() {
	    var name2 = "Jack";
	    console.log(name1); //Tom
	    console.log(name2); //Jack
	}
	fn();
	console.log(name1); //Tom
	console.log(name2); //ReferenceError: name2 is not defined

以上就是name1在全局作用域所以可以在任何地方访问，name2在局部作用域，仅可以在函数中访问。


## 2.2作用域链

全局作用域和局部作用域中变量的访问权限，其实是由作用域链决定的。  
每进入一个新的执行环境就会新建一个用于搜索函数和属性的作用域链，作用域链是函数被创建时的作用域对象集合。作用域链可以保证对执行环境有权访问的属性和函数有序访问。

	var name1 = "Tom";
	function outter() {
	    var name2 = "outter-Jack";
	    function inner() {
	        console.log(name1); //Tom
	        console.log(name2); //outter-Jack
	        var T = name1;
	        name1 = name2;
	        name2 = T;
	        console.log(name1); //outter-Jack
	        console.log(name2); //Tom
	    }
	    inner();
	    console.log(name1); //outter-Jack
	    console.log(name2); //Tom
	}
	outter();
	console.log(name1); //outter-Jack

outter的作用域链包括二个对象：自己的变量对象----》全局的变量对象
inner的作用域包括三个对象： 自己的变量对象-----》outter（）局部环境的变量对象-----》全局环境的变量对象。   

当前作用域可以修改上一级作用域的变量数据。    
如inner（）内name1与name2互换。