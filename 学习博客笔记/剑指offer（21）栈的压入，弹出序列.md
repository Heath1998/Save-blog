(19.1.19)
# 剑指offer（21）栈的压入，弹出序列

**题目描述**   
**输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）**   


思想： 增加一个辅组数组sck = [], 遍历pushV时sck.push[pushV[i]]，并声明id = 0. 之后就while遍历判断sck的长度是否为0，和sck的顶元素是否与popV[id]的第一个元素相等，即底层元素。是的话就进行sck的顶元素出栈，且id+1。等到全部遍历判断sck是否为空，是就是正确。否则为假。


代码：  

	function IsPopOrder(pushV, popV)
	{
	    let sck = [];
	    let id = 0;
	    for(var i = 0; i < pushV.length; ++i)
	    {
	        sck.push(pushV[i]);
	        while(sck.length && sck[sck.length - 1] == popV[id])
	        {
	            ++id;
	            sck.pop();
	        }
	    }
	    return sck.length == 0;
	}
	
	let a = [1,2,3,4,5];
	let b = [4,3,5,1,2];
	
	console.log(IsPopOrder(a, b));