（18.12.26）
# js中apply和call的使用方法

call：调用一个函数在一个指定的执行上下文中，和提供参数。  

	function fn(more) {
	    console.log(this.age);
	    console.log(this.name);
	    console.log(more);
	}    
	function A(age,name) {
	    this.age = age;
	    this.name = name;
	}
	var Aobj = new A(11,"Tom");
	fn.call(Aobj,"more");

以上代码就是调用fn函数在Aobj的执行上下文中执行的结果为   
11  
Tom  
more  

apply：调用一个函数在一个指定的执行上下文中，和提供参数是数组一般为arguments。  


	function A(age,name) {
	    this.age = age;
	    this.name = name;
	}
	function B(age,name,sex) {
	    this.sex = sex;
  		console.log(arguments);
	    A.apply(this, arguments);
	}
	var Bobj = new B(11,"tom","男");
	console.log(Bobj.age);
	console.log(Bobj.name);
	console.log(Bobj.sex);

结果为：  
11  
Tom  
男  

 arguments为当前的函数参数为：  
[Arguments] { '0': 11, '1': 'tom', '2': '男' }