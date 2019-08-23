（19.2.22）
# 剑指offer（39）数组中只出现一次的数字

**题目描述   
一个整型数组里除了两个数字之外，其他的数字都出现了偶数次。请写程序找出这两个只出现一次的数字。**

最笨的方法就是遍历了。之后检查谁只出现


代码如下


	function FindNumsAppearOnce(array)
	{
	    var obj = {};
	    for(var i=0;i<array.length;i++)
	    {
	        if(obj[array[i]]) {
	            obj[array[i]]++;
	        }
	        else {
	            obj[array[i]] = 1;
	        }
	    }
	    var brr = [];
	    for(var a in obj)
	    {
	        if(obj[a] == 1)
	            brr.push(a);
	    }
	    return brr;
	}