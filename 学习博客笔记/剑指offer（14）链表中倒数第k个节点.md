（19.1.9）
# 剑指offer（14）链表中倒数第k个节点

**题目描述**
**输入一个链表，输出该链表中倒数第k个结点。**

可以有两个指针pNode1和pNode2指向头

p2就根据k遍历到第k个位置，之后p1与p2同时遍历到p2.next = null结束

长度刚好是整条长度，p1是1+length-k即第k个

代码：  


	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function FindKthToTail(head, k)
	{
	    // write code here
	    if(head==null||k<=0) return null;
	    var pNode1 = head, pNode2 = head;
	    while(--k) {
	        if(pNode2.next != null) {
	            pNode2 = pNode2.next;
	        }
	        else {
	            return null;
	        }
	    }
	    while(pNode2.next != null) {
	        pNode2 = pNode2.next;
	        pNode1 = pNode1.next;
	    }
	    return pNode1;
	}
	var a = new  ListNode(1);
	var b = new ListNode(2);
	var c =new ListNode(3);
	a.next = b;
	b.next = c;
	console.log(FindKthToTail(c,1));