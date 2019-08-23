（19.2.21）
# 剑指offer（38）二叉树的深度

**题目描述   
输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。**


这题较简单，就是简单的从下往上时比较下左右高度。


	 function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	
	function TreeDepth(pRoot)
	{
	    if(pRoot == null)
	        return 0;
	    var left = TreeDepth(pRoot.left);
	    var right = TreeDepth(pRoot.right);
	    return left >right ? left+1:right+1;
	}

