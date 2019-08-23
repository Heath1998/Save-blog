（19.3.11）
# 剑指offer（56）二叉树的下一个节点

**题目描述
给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。**


![](https://uploadfiles.nowcoder.com/files/20171225/773262_1514198075109_20151104234034251)
1、有右子树的，那么下个结点就是右子树最左边的点；  
 2、没有右子树的，也可以分成两类，a)是父节点左孩子（eg：N，I，L） ，那么父节点就是下一个节点 ； b)是父节点的右孩子（eg：H，J，K，M）找他的父节点的父节点的父节点...直到当前结点是其父节点的左孩子位置。如果没有eg：M，那么他就是尾节点。

代码如下：


	function TreeLinkNode(x){
	    this.val = x;
	    this.left = null;
	    this.right = null;
	    this.next = null;
	}
	
	
	function GetNext(pNode)
	{
	    if(pNode==null) return null
	    else if(pNode.right) {
	        var pright = pNode.right;
	        while(pright.left){
	            pright = pright.left;
	        }
	        return pright;
	    }
	    else {
	        while(pNode.next&&pNode.next.right == pNode){
	            pNode = pNode.next;
	        }
	        if(pNode.next){
	            return pNode.next;
	        }
	        else
	        return null;
	    }
	
	}




