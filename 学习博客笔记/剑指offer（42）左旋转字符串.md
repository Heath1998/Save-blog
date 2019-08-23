（19.2.25）

# 剑指offer（42）左旋转字符串


**题目描述    
汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！**


主要记住string对象中的方法如substring(start,end)截取从start到end-1的数组不影响原数组


代码如下：

	function LeftRotateString(str, n)
	{
	    if(str==null||str.length==0) return '';
	    var right = str.substring(0,n);   
	    var left = str.substring(n,str.length);
	    return left + right;
	}
	console.log(LeftRotateString("abcdef",3));


