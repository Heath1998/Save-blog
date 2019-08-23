（19.3.11）
# 剑指offer（57）对称的二叉树

**题目描述
请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。**


思路，比较pRoot的左子树与它的右子树相等，再递归左子树的左子树与右子树的右子树相等。  
右子树的左子树与左子树的右子树相等。  

代码如下：

	 function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	function isSymmetrical(pRoot)
	{
	    if(pRoot==null){
	        return true;
	    }
	    return compare(pRoot.left,pRoot.right);
	}
	
	
	function compare(left,right)
	{
	    if(left==null&&right==null)
	        return true;
	    if(left!=null&right!=null)
	    {
	        if(left.val == right.val)
	        {
	            return compare(left.right,right.left)&&compare(left.left,right.right);
	        }
	    }
	    return false;
	}