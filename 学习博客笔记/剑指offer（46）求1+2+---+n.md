（19.3.1）

# 剑指offer（46）求1+2+---+n

**题目描述   
求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。**


数学问题：n*(n+1)/2

代码如下：

	function Sum_Solution(n)
	{
	    return n*(1+n)/2;
	}