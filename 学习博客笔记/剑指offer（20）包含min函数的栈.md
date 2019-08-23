（19.1.18）
# 剑指offer（20）包含min函数的栈

**定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））**。

思路很简单，就是简单的运用栈的操作，先全部取出，再存入

代码如下：

	var stack = [];
	
	function push(node)
	{
	    // write code here
	    stack.push(node);
	}
	function pop()
	{
	    // write code here
	    return stack.pop();
	}
	function top()
	{
	    // write code here
	    return stack.peek();
	}
	function min()
	{
	    // write code here
	    if(stack.length == 0)
	        return null;
	    var minNum = stack.pop();
	    var stack2 = [];
	    stack2.push(minNum);
	    while(stack.length)
	    {
	        var x = stack.pop();
	        stack2.push(x);
	        if(x < minNum)
	            minNum = x;
	    }
	    while(stack2.length)
	    {
	        stack.push(stack2.pop());
	    }
	    return minNum;
	}


