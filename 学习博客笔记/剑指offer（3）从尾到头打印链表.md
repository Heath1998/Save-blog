（18.12.28）
# 剑指offer （3）从尾到头打印链表


我发现写来写去还是js比较好写，java我写了好久，连ArrayList的逆置函数都要自己写，所以我决定为了节省时间以后只用js写吧。等到蓝桥杯考前一星期我在用java熟悉一下语法等吧。把丢的捡回来。

话不多说，进入今天的题目：   

**时间限制：1秒 空间限制：32768K 热度指数：684471**   
**本题知识点： 链表**    

**题目描述**    
**输入一个链表，按链表值从尾到头的顺序返回一个ArrayList**。

从题可知最简单的方法遍历一遍，边存储便遍历，最后逆置数组即可。   


	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function printListFromTailToHead(head)
	{
	    var list = new Array();
	    var p =head;
	    while(p != null)
	    {
	        list.push(p.val);
	        p = p.next;
	    }
	    return list.reverse();
	}
	
	var a = new ListNode(1);
	var b = new ListNode(2);
	var c = new ListNode(3);
	a.next = b;
	b.next = c;
	
	console.log(printListFromTailToHead(a));   


写完了，我要好好复习互联网概论了，快考试了，明天接着一题。