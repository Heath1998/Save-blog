（19.3.8）

# 剑指offer（53）字符流中第一个不重复的字符

**题目描述     
请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。     
输出描述:      
如果当前字符流没有存在出现一次的字符，返回#字符。**


代码如下：


	var obj;
	 
	function Init()
	{
	    // write code here
	    obj={};
	}
	//Insert one char from stringstream
	function Insert(ch)
	{
	    // write code here
	    if(ch in obj){
	        obj[ch]++;
	    }else{
	        obj[ch]=1;
	    }   
	}
	//return the first appearence once char in current stringstream
	function FirstAppearingOnce()
	{
	    // write code here
	   for(var key in obj){
	    if(obj[key]==1){
	        return key;
	    }
	   }
	    return '#';
	}