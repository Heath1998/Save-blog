（19.1.8）
# 剑指offer（13）调整数组顺序使奇数位于偶数前

**题目描述**    
**输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变**。


这题主要就是插入排序，从0往上找，找到奇数，再向下找所有偶数使得j与j-1换位。  


贴代码把：  


	function reOrderArray(array)
	{
	    // write code hereth
	    for(var i = 0; i < array.length; ++i)
	    {
	        var tmp = array[i];
	        if(tmp % 2 == 1) {
	            for(var j = i; j > 0; --j) {
	                if(array[j - 1] % 2 == 0) {
	                    var t = array[j];
	                    array[j] = array[j-1];
	                    array[j-1] = t;
	                }
	            }
	        }
	    }
	    return array;
	}
	var a = [1,2,4,7];
	
	console.log(reOrderArray(a));