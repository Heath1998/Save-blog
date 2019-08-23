（19.1.4）
# 剑指offer（9）变态跳台阶

**题目描述**    
**一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。**     

这道题可以这样想设跳n级的台阶有f（n）种跳法。   

假设跳1级，则剩下n-1级的可能   
跳2级，剩下n-2级的可能   
.........    

有f（n） = f（n-1） + f（n-2）+ ...+ f(1);   
f(n-1) = f(n-2) +f(n-3) +...+f(1);

则f(n) = 2*f(n-1)

所以代码很简单： 


	function jumpFloorII(number)
	{
	   if(number == 1 || number == 0 ||number == 2) {
	       return number;
	   }
	   var num = 2;
	   for(var i = 3; i <= number; ++i)
	   {
	       num = num * 2;
	   }
	   return num;
	}
	
	console.log(jumpFloorII(5));