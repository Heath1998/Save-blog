(19.2.12)

# 剑指offer（28）数组中出现次数超过一半的数字

状态开始回来可以开始写算法题了。

**题目描述  
数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。**

这题较简单，可以用“分形叶”的思想，但我做的时候没考虑那么多，用了个笨办法。

代码如下:
	
	function MoreThanHalfNum_Solution(numbers)
	{
	    // write code here
	    var obj = {};
	    var len = numbers.length/2;
	    for(var i=0; i<numbers.length; ++i) {
	        if(!obj[numbers[i]]) {
	            obj[numbers[i]] = 1;
	        }
	        else {
	            obj[numbers[i]]++;
	        }
	    }
	    for(var item in obj) {
	        if(obj[item] > len) 
	            return item;
	    }
	    return 0;
	}
	
	var arr = [1,2,3];
	console.log(MoreThanHalfNum_Solution(arr));