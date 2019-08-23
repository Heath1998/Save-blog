（19.2.15）
# 剑指offer（31）整数中1出现的次数

快上学了，努力复习

**题目描述  
求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。**

这个题目一开始没有注意效率，后来看了别人的代码发现还是要注意效率的，就是找规律。

我一开始的代码思想就是for遍历里面再while遍历每一位

代码如下：

	
	function NumberOf1Between1AndN_Solution(n)
	{
	    // write code here
	    var count = 0;
	    var num;
	    for(var i = 0;i<=n;i++) {
	        num = i;
	        while(num)
	        {
	            if(num%10 == 1) {
	                count++;
	            }
	            num = Math.floor(num/10);
	        }
	    }
	    return count;
	}
	
	console.log(NumberOf1Between1AndN_Solution(13));