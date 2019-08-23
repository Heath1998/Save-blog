（19.3.9）
# 剑指offer（54）链表中环的入口结点

**题目描述
给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。**

思路：设p1，p2两点，p1每次走一步，p2每次走两步，则当p1和p2重合时，p2已经走了一圈了。
设p1走了x距离，则p2为2x，又p2多走一圈。设圈的长度为n。则2x = x + n，即x=n。   
则设p3=pHead，p3和p1每次走一步，当他们相遇时就是圈的起点，因为x =n。


	
	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function EntryNodeOfLoop(pHead)
	{
	    var p1=pHead,p2=pHead;
	    if(p1==null||p2==null)
	        return null;
	    while(p1!=null&&p2.next!=null)
	    {
	        p1 = p1.next;
	        p2 = p2.next.next;
	        if(p1 == p2)
	        {
	            p2 = pHead;
	            while(p2!=p1)
	            {
	                p1 = p1.next;
	                p2 = p2.next;
	            }
	            if(p1==p2)
	            return p1;
	        }
	    }
	    return null;
	}
	var a = new ListNode(2);
	var b = new ListNode(3);
	var c = new ListNode(3);
	a.next = b;
	b.next = c;
	console.log(EntryNodeOfLoop(a));