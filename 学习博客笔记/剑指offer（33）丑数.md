(19.2.18)

# 剑指offer（33）丑数

**题目描述  
把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。**

这题丑数1，2，3，4，5，6，8，9这样。所以可以用一个数组存放丑数，这个数组中的数*2，*3，*5之中必定有一个新的最小的数刚好大于当前最大丑数。

分析：在数组中必定有一个丑数M2， 在它之前的数 * 2 都小于当前最大丑数， 在它之后的数 * 2都大于当前最大丑数， 同样有M3, M5。当是我看别人的代码不知道为什么当前t2，t3，t5的个数就是刚好左边小于当前最大丑数，右边大于当前最大丑数的位置。算了，等时间慢慢理解吧。



	function GetUglyNumber_Solution(index)
	{   
	    if(index == 1||index == 0)
	    return index;
	    var t2=0,t3=0,t5=0;
	    var min;
	    var arr = [1];
	    for(var i=1;i<index;i++)
	    {
	        arr[i] = Math.min(arr[t2]*2,Math.min(arr[t3]*3,arr[t5]*5));
	        if(arr[i]==arr[t2]*2) t2++;
	        if(arr[i]==arr[t3]*3) t3++;
	        if(arr[i]==arr[t5]*5) t5++;
	    }
	    return arr[index-1];
	}
	
	console.log(GetUglyNumber_Solution(3));