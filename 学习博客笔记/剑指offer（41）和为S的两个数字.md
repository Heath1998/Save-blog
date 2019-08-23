（19.2.24）
# 剑指offer（41）和为S的两个数字

**题目描述    
输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。    
输出描述:    
对应每个测试案例，输出两个数，小的先输出。**



这题较简单最笨的方法遍历，最后返回第一二个数即可，因为正方形的面积最大


代码如下：


	function FindNumbersWithSum(array, sum)
	{
	    var result = [];
	    for(var i=0;i<array.length;i++)
	    {
	        for(var j = i+1;j<array.length;j++)
	        {
	            if(array[i]+array[j] == sum)
	            {
	                result.push(array[i]);
	                result.push(array[j]);
	                
	            }
	            else if(array[i]+array[j] > sum)
	                break;
	        }
	    }
	    if(result.length == 0)
	    return [];
	    return [result[0],result[1]];
	}
	
	var arr =[1,2,3,4];
	console.log(FindNumbersWithSum(arr,5));