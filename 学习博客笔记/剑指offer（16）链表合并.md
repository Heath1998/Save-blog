（19.1.11）
# 剑指offer（16）链表合并

**题目描述**   
**输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则**。

第一想到的就是递归：

pHead1和pHead2为两者的头指针，比较它的pHead1.val和pHead2.val若pHead1.val小，就可以递归pHead1.next = Merge（pHead1.next, pHead2.next）反之， pHead2.next = Merg(pHead1,pHead2.next);



代码如下：   

	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function Merge(pHead1, pHead2)
	{
	    // write code here
	    if(pHead1 == null) {
	        return pHead2;
	    }
	    if(pHead2 == null) {
	        return pHead1;
	    }
	    if(pHead1.val <= pHead2.val) {
	        pHead1.next = Merge(pHead1.next, pHead2);
	        return pHead1;
	    }
	    else {
	        pHead2.next = Merge(pHead1,pHead2.next);
	        return pHead2;
	    }
	}



非递归也是这个思想：

先创建一个root=head = new ListNode（-1）的根节点，之后while遍历pHead1和pHead2为非空，若pHead1.val<pHead2.val，则pHead1当前节点插入根节点head.next上，之后pHead1 = pHead1.next.反之，也差不多。


代码如下： 



	function MergeRecursion(pHead1, pHead2)
	{
	    // write code here
	    var head = new ListNode(-1);
	    var root = head;
	    head.next = null;
	    while(pHead1 !== null && pHead2 !== null) {
	        if(pHead1.val <= pHead2.val) {
	            head.next = pHead1;
	            head = pHead1;
	            pHead1 = pHead1.next;
	        }
	        else {
	            head.next = pHead2;
	            head = pHead2;
	            pHead2 = pHead2.next;            
	        }
	    }
	    if(pHead1 != null) {
	        head.next = pHead1;
	    }
	    if(pHead2 != null) {
	        head.next = pHead2;
	    }
	    return root.next;
	}