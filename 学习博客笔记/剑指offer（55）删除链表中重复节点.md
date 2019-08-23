（19.3.10）
# 剑指offer（55）删除链表中重复节点


题目描述
在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

这题暴力即可

	function ListNode(x){
	    this.val = x;
	    this.next = null;
	}
	function deleteDuplication(pHead)
	{
	    var arr = [];
	    var List = new ListNode(0);
	    List.next=pHead;
	    var p = List.next;
	    for(var i=0;p!=null;i++){
	        arr[i]=p.val;
	        p=p.next;
	    }
	    var p2=List;
	    while(p2.next!=null){
	        if(arr.indexOf(p2.next.val)!=-1&&arr.indexOf(p2.next.val)!=arr.lastIndexOf(p2.next.val)){
	            p2.next = p2.next.next;
	        }
	        else{
	            p2 = p2.next;
	        }
	    }
	    return List.next;
	}
	
	var a = new ListNode(1);
	var b = new ListNode(2);
	var c = new ListNode(3);
	var d = new ListNode(3);
	a.next = b;
	b.next =c;
	c.next =d;
	console.log(deleteDuplication(a));