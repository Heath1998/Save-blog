（19.3.5）
# 剑指offer（50）构建乘积数组
 
**题目描述   
给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。**

思路：杨辉三角，只有自己是1

代码如下：

	function multiply(array)
	{
	    if(array.length==0)
	        return [];
	    var brr = [];
	    brr[0]=1;
	    for(var i=1;i<array.length;i++)
	    {
	        brr[i] = brr[i-1]*array[i-1];
	    }
	    var temp = 1;
	    for(var j = array.length-2;j>=0;j--)
	    {
	        temp = temp * array[j+1];
	        brr[j] =brr[j]*temp;
	    }
	    return brr;
	}
	
	console.log(multiply([1,2,3]))