（19.3.7）
# 剑指offer（52）表示数值的字符串


**题目描述
请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。**

代码如下：
	
	//s字符串
	function isNumeric(s)
	{
	    //先判断是否全是数字，且不是NaN
	    if(typeof Number(s) == 'number' && !isNaN(Number(s))) {
	        return true;
	    }
	    //判断e且只有一个e
	    if(s.indexOf('e')!=-1&&s.lastIndexOf('e') - s.indexOf('e')==0)
	    {
	        if(typeof s.split('e')[0] == 'number'&& typeof s.split('e')[1] == 'number')
	            return true;
	    }
	    //判断E
	    if(s.indexOf('E')!=-1&&s.lastIndexOf('E') - s.indexOf('E')==0)
	    {
	        if(typeof s.split('E')[0] == 'number'&& typeof s.split('E')[1] == 'number')
	            return true;
	    }
	    return false;
	}
	
	console.log(isNumeric('11'))