（19.1.23）
# 剑指offer（26）二叉搜索树与双向链表

**输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。**


用到了中序遍历最为简单的方法。

需要一个lastNode指针。将root先左递归返回latNode，lastNode为左子树最后一个节点，
中间做链接，之后再遍历右子树，把根lastNode = root 作为参数传入，
返回convertNode(root.right, lastNode)在这个过程中会做root和右子树的链接。

代码如下：

	function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	function Convert(pRootOfTree)
	{
	    // write code here
	    if(pRootOfTree==null){
	        return null;
	    }
	    var lastNode=null;
	    lastNode=convertNode(pRootOfTree,lastNode);
	    var head=lastNode;
	    while(head && head.left){//循环到头部
	        head=head.left;
	    }
	    return head;
	}
	function convertNode (root,lastNode){
	    if(root==null) return;
	    if(root.left){//左子树
	        lastNode=convertNode(root.left,lastNode)
	    }
	    root.left=lastNode;
	    if(lastNode){
	        lastNode.right=root;
	    }
	    lastNode=root;
	    if(root.right){//右子树
	        lastNode=convertNode(root.right,lastNode)
	    }
	    return lastNode;
	}
	
	var a = new TreeNode(2);
	var b = new TreeNode(1);
	var c = new TreeNode(3);
	a.left = b;
	a.right = c;
	// console.log(a);
	var d = Convert(a)
	console.log(d);

