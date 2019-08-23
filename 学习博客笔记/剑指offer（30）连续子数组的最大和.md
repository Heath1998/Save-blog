(19.2.14)

# 剑指offer（30）连续子数组的最大和

刷题刷题，

**题目描述  
HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)**

这题如果没想到点可以说要点难度。  
核心思想就是动态规划。   

首先确定数组array

F(i) = 是以array[i]结尾的最大连续数字和   
F(i) = Math.max(F(i-1)+array[i],array[i])
     

res就是当前子数组中最大连续和   
res = Math.max(F(i),res);

这样保证每次都是有当前子数组的最大连续子序列和res，且每个F(i)都是包含当前i保证计算连续子序列。

代码如下：


	function  FindGreatestSumOfSubArray( array) {
	    var max = array[0];
	    var res = array[0];
	    for(var i=1;i<array.length;i++) {
	        max = Math.max(max+array[i],array[i]);
	        res = Math.max(max,res);
	    }
	    return res
	}
	var arr = [-1,2,3,-4];
	console.log(FindGreatestSumOfSubArray(arr));
