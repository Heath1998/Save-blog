（18.12.28）
# 剑指offer牛客（2）替换空格

这个较为简单。

**时间限制：1秒 空间限制：32768K 热度指数：764593**    
**本题知识点： 字符串**    
**题目描述**    
**请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy**。    


主要还是用   

java的StringBuffer类型： replace（int a,int b, String str）替代a到b变成str   
java的代码如下：

    public static String replaceSpace(StringBuffer str) {
    	for(int i=0; i < str.length(); ++i)
    	{
    		if(str.charAt(i) == ' ')
    		{
    			str.replace(i, i+1, "%20");
    		}
    	}
    	return new String(str);
    }

js的代码如下：较为简单直接用正则：   

	function replaceSpace(str)
	{
	    return str.replace(/ /g,"%20");
	}
	
	var a = " Str a";
	
	console.log(replaceSpace(a));