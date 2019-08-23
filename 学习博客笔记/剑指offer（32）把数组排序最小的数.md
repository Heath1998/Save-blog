（19.2.16）

# 剑指offer（32）把数组排序最小的数


**题目描述  
输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。**


这题思路就是小的排前面如“12”，“145”，则“12”排前面，和“32”，“321”则321排前面。
比较可以用a+b和b+a之后比较那种可能小a+b小则a放前面，b+a小就则数组b放前面


代码：

	function PrintMinNumber(numbers)
	{
	    // write code here
	    numbers.sort(compare);
	    var str ="";
	    for(var i of numbers)
	        str = str+i;
	    return str;
	}
	
	function compare(a, b) {
	    var str1=a+""+b;
	    var str2=b+""+a;
	    for(var i =0 ;i<str1.length;i++)
	    {
	        if(str1[i]>str2[i])
	            return 1;
	        if(str1[i]<str2[i])
	            return -1;
	    }
	    return 0;
	}
	
	var arr=["123","1","23"];
	
	console.log(PrintMinNumber(arr))