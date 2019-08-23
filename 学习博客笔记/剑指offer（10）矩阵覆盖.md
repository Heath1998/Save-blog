(19.1.5)
# 剑指offer（10）矩阵覆盖

**题目描述**  
**我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？**   

这题其实就是青蛙跳。   
可以这样想矩阵可以竖着摆，这样就是跳2个台阶，横着摆就是跳一个台阶。观察规律发现   
f(n)=f(n-1)+f(n-2)    

所以他是一个斐波那契数列。   上代码：  


	function rectCover(number)
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