(19.2.20)

# 剑指offer（37）数字在排序数组中出现的次数

**题目描述   
统计一个数字在排序数组中出现的次数。**

不得不说javascript做这种题真的很方便，函数都是封装好的。这题我认为思想就是找到第一个出现的数和最后出现的数位置相减+1即可。

代码如下：


	function GetNumberOfK(data, k)
	{
	    if(data.indexOf(k) == -1)
	        return 0;
	    return data.lastIndexOf(k) - data.indexOf(k) + 1; 
	}
	
	var arr = [1,2,2,3];
	console.log(GetNumberOfK(arr,2));