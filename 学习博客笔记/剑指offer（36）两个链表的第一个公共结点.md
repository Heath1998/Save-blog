（19.2.19）

# 剑指offer（36）两个链表的第一个公共结点

感觉涉及到算法优化的就做不出来了，哎做几个简单题吧。

**题目描述
输入两个链表，找出它们的第一个公共结点。**


这题暴力双for的思想


代码如下：

	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function FindFirstCommonNode(pHead1, pHead2)
	{
	    var p1 = pHead1;
	    while(p1)
	    {
	        if(check(p1,pHead2))
	            return p1;
	        p1 = p1.next;
	    }
	    return null;
	}
	function check(p, p2) 
	{
	    while(p2)
	    {
	        if(p == p2)
	            return true;
	        p2 = p2.next;
	    }
	    return false;
	}