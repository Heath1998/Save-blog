（18.12.26）
# js的数据类型判断和深/浅拷贝的理解

js的数据类型分为基本数据类型和引用数据类型。   
基本数据类型包括五个Number，String，Boolean，undefined，null。   
引用数据类型就有很多如Array，Object，Function等等。   


判断数据类型有以下三个方法：   
一，typeof   
使用

	var a = [];
	var b = null;
	var c = 1;
	console.log(typeof a); //object
	console.log(typeof b); //object
	console.log(typeof c); //number
typeof只适用于基本数据类型，而且用null时会判定为object

二，instanceof
使用

	var a = [];
	var b = null;
	var c = "1";
	console.log( a instanceof Array); //true
	console.log(b instanceof Object); //false
	console.log(c instanceof String); //false


instanceof一般用来判断引用数据类型如object和array等


三，最准确的判断方式Object.prototype.toString.call(a)

	var a = [];
	var b = null;
	var c = "1";
	console.log(Object.prototype.toString.call(a)); //[object Array]
	console.log(Object.prototype.toString.call(b)); //[object Null]
	console.log(Object.prototype.toString.call(c)); //[object String]

它能准确的判断所有类型，toString返回这个对象的字符串，用prototype是因为Array和String原型中重写toString函数。


## 拷贝

一，深拷贝
就是复制值有一个新的内存，对目标完全拷贝，一者该变，不会影响。一般用在基本数据类型

	var a = 123;
	var b = a;
	console.log(a); //123
	console.log(b);  //123
	b = 456;
	console.log(a);//123
	console.log(b);//456

二，浅拷贝
就是只复制引用，没有开辟新空间，一者改变，两者一起变。一般用在引用数据类型

	var a = [1,2,3];
	var b = a;
	console.log(a); //[ 1, 2, 3 ]
	console.log(b);  //[ 1, 2, 3 ]
	b.push(4);
	console.log(a); //[ 1, 2, 3, 4 ]
	console.log(b); //[ 1, 2, 3, 4 ]