（19.1.11)
# 剑指offer（17）树的子结构

**题目描述**   
**输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）**


思修很简单：遍历A树，每个A节点与B对比，对比函数为solution。

代码如下：

	 function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	
	function HasSubtree(pRoot1, pRoot2)
	{
	    // write code here
	    if(pRoot1 == null || pRoot2 == null) {
	        return false;
	    }
	        return solution(pRoot1, pRoot2) ||
	        HasSubtree(pRoot1.left, pRoot2) ||
	        HasSubtree(pRoot1.right,pRoot2);
	
	}
	function solution(pRoot1, pRoot2) {
	    if(pRoot2 == null)
	        return true;
	    if(pRoot1 == null)
	        return false;
	    if(pRoot1.val == pRoot2.val) {
	        return solution(pRoot1.left, pRoot2.left) &&
	        solution(pRoot1.right, pRoot2.right);
	    }
	    else
	    return false;
	}