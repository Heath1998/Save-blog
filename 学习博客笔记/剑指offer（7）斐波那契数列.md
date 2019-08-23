（19.1.2）
# 剑指offer（7）斐波那契数列

今天刚好考完试，会写但是也仅仅会写，对这门课兴趣基本没有，所以基本低保过，不说了做题。  

这是一个简单的题就是a0=0，a1=1，an=an-1+an-2.   
主要就是要从下到上，这样可以省很多时间，先把获取的存到数组中，要的话直接取。   


上代码：   

	
	function down(n, x, arr) {
	    
	    if(n == x ) 
	    {
	        arr[x] = arr[x-1] + arr[x-2]; 
	    }
	    else if(x === 0 || x===1)
	    {
	        down(n, x+1, arr);
	    }
	    else 
	    {
	        arr[x] = arr[x-1] + arr[x-2];
	        down(n, x+1,arr);
	    }
	}
	
	function Fibonacci(n)
	{
	    var arr = [];
	    arr[0] = 0;
	    arr[1] = 1;
	    if(n === 1 || n === 0)
	        return n;
	    down(n, 0 , arr);
	    return arr[n];   
	}
	
	console.log(Fibonacci(2));
	console.log(Fibonacci(0));
	console.log(Fibonacci(12));