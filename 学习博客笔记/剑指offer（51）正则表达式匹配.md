(19.3.6)

# 剑指offer（51）正则表达式匹配

题目描述   
请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配


代码如下：

	//s, pattern都是字符串
	function match(s, pattern)
	{
	    var sindex=0,pindex=0;
	   return  matchCode(s,pattern,sindex,pindex); //要初始化sindex，和pindex
	}
	
	function matchCode(s,p,sindex,pindex)
	{
	    if(s.length==sindex && p.length == pindex)  //如果字符串和匹配串都到末尾则成功
	        return true;
	    if(s.length!==sindex && p.length==pindex)   //如果字符串已经走完，但匹配串还没走完，就返回失败
	        return false;
	    if(p[pindex+1]!=='*')  //判断匹配串是否下一个为*
	    {
	        //是的话
	        if(p[pindex] == s[sindex] || (s[sindex]!==undefined&&p[pindex]=='.')) //是的话一起加1
	            return matchCode(s,p,sindex+1,pindex+1);
	        else
	            return false;
	    }
	    else {
	        //不是的话，判断是相等，或则为.则有两种可能，第一种接着*号设置为匹配0个字符，所以+2.  第二种是字符串继续匹配。
	        if(p[pindex] == s[sindex] || (s[sindex]!==undefined&&p[pindex]=='.'))
	            return matchCode(s,p,sindex,pindex+2) || matchCode(s,p,sindex+1,pindex);
	        else {
	            //否则只把*看成匹配0个
	            return matchCode(s,p,sindex,pindex+2);
	        }
	    }
	}

    console.log( match('a', 'a'))


