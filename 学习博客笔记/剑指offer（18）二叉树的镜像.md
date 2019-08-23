(19.1.13)
# 剑指offer（18）二叉树的镜像

 
**操作给定的二叉树，将其变换为源二叉树的镜像。**   
**输入描述:**   
**二叉树的镜像定义：源二叉树 **   

    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5


这题主要思想就是递归加交换。

代码如下：

	function Mirror(root)
	{
	    // write code here
	    if(root === null) return null;
	    if(root.left === null && root.right === null) return root;
	    var temp = root.left;
	    root.left = Mirror(root.right);
	    root.right = Mirror(temp);
	    return root;
	}
	
	var a = new TreeNode(1);
	var b = new TreeNode(2);
	var c = new TreeNode(3);
	a.left = b;
	a.right = c;
	console.log(a);
	var d = Mirror(a);
	console.log(d);