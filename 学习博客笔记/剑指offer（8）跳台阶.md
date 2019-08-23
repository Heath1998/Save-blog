（19.1.3）
# 剑指offer（8）跳台阶

今天起床晚了忘记去上复习课了，太冷了，算了还是做题吧。    

**题目描述**   
**一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。**
    
一开始我也没什么思路，后来动笔画了画发现这题就是找规律，是斐波那契数列，从3开始就是a3 = a2+a1了。   

则可以写出简单的代码：   

	function jumpFloor(number)
	{
	   if(number == 1 || number == 0 ||number == 2) {
	       return number;
	   }
	   var one = 1;
	   var two = 2;
	   var num;
	   for(var i = 3; i<=number; ++i)
	   {
	       num = one + two;
	       one = two;
	       two =num;
	   }
	   return two;
	}
	
	console.log(jumpFloor(4));

从下往上运算会快点