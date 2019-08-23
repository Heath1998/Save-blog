 （19.3.2）
# 剑指offer（47）不用加减乘除做加法

题目描述  
写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

代码如下：


	function Add(num1, num2)
	{
	    while(num2) 
	    {
	        var sum = num1^num2; // 没进位的加法
	        var jia = (num1&num2)<<1; // 进位的二进制
	        num1 = sum;
	        num2 = jia;
	    }
	    return num1;
	}
