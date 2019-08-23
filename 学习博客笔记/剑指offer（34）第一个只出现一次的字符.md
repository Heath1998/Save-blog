（19.2.18）

# 剑指offer（34）第一个只出现一次的字符

**题目描述   
在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.**

这题我没什么好办法，只有遍历，主要是js的对象遍历是按加入顺序的。


代码如下：


	function FirstNotRepeatingChar(str)
	{
	    if(str=="")
	    return -1;
	    var obj = {};
	    for(var i=0;i<str.length;i++)
	    {
	        if(obj[str[i]]) {
	            obj[str[i]].num ++;
	        }
	        else {
	            obj[str[i]] = {
	                "key": i,
	                "num": 1
	            };
	        }
	    }
	    console.log(obj);
	    for(var j in obj)
	    {
	        if(obj[j].num==1)
	            return obj[j].key;
	    }
	}
	
	var c = "asdasdsadsaaaaaaaaaax";
	FirstNotRepeatingChar(c);
