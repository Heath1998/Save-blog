（18.12.29）
# 剑指offer（4）重建二叉树

**题目描述**    
**输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回**。    


这题主要考二叉树，
前序主要是    
root   
left   
right    

中序是   
left   
root   
right    

举个简单例子；
1，2，4，5，3，6，7是前序；
4，2，5，1，6，3，7是中序；

则1是root时找中序1的位置，则4，2，5，1在左，6，3，7在右；   
左边递归就是前序2，4，5。   中序是4，2，5；
    
右边递归是前序3，6，7.  中序是6，3，7；   

一直递归到没左右节点上代码吧：   

	function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	function reConstructBinaryTree(pre, vin)
	{
	    if(!pre || !vin || pre.length != vin.length || pre.length == 0 || vin.length == 0)
	        return null;
	    return recursion(pre, vin);
	}
	function recursion(pre, vin) {
	    var rootVal = pre[0];
	    var rootTree = new TreeNode(rootVal);
	    var rootIndex = 0;
	    while(rootVal !== vin[rootIndex]) {
	        rootIndex++;
	    }
	    if(rootIndex > 0) {
	        rootTree.left = recursion(pre.slice(1, rootIndex + 1), vin.slice(0, rootIndex));
	    }
	    if(rootIndex < vin.length - 1) {
	        rootTree.right = recursion(pre.slice(rootIndex + 1, pre.length + 1), vin.slice(rootIndex + 1, vin.length + 1));
	    }
	    return rootTree;
	}

较为简单