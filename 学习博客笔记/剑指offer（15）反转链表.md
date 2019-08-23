（19.1.11）
# 剑指offer（15）反转链表

这是一道链表反转题。

我首先想到非递归的方法。需要用到pre（已经反转的部分的头结点），pHead（还未反转的一部分的头部指针），next为当前头的下一个节点。就这样循环一遍就分解出一个头结点，并使他的.next指向pre
当pHead为空时结束。

非递归代码：

	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function ReverseList(pHead)
	{
	    // write code here
	    var pre = null, next = null;
	    while(pHead) {
	        next = pHead.next;  //取头结点的下一个节点
	        pHead.next = pre;   //头结点的.next指向已经逆置的部分pre
	        pre = pHead;     //pre的pHead使之逆置部分改变
	        pHead = next;    //头指针指向下一个节点
	    }
	    return pre;
	}


递归的方法：

先判断当前phead和.next是不是null，是就返回当前，不是就要再往下遍历，遍历到最后，之后
newNode就是后面的节点为新的逆置节点，newNode.next = pHead, pHead.next =null

代码如下： 

	function ReverseListRecursion(pHead)
	{
	    // write code here
	    if(pHead == null|| pHead.next == null) {
	        return pHead;
	    }
	    else {
	        var newNode = ReverseListRecursion(pHead.next);
	        newNode.next = pHead;
	        pHead.next = null;
	    }
	    return newNode;
	}
	
	var a = new ListNode(1);
	var b = new ListNode(2);
	var c = new ListNode(3);
	a.next = b;
	b.next = c;
	console.log(a);
	console.log(ReverseListRecursion(a));