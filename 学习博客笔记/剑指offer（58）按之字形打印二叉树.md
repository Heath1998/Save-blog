(19.3.11)
# 剑指offer（58）按之字形打印二叉树


**题目描述
请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。**


思路：就是BFS，while中套个for每次把外部的while取完


代码如下：

	 function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	function Print(pRoot)
	{
	    if(pRoot === null){
	        return []
	    }
	    var result = [];
	    var saveOuter = [];
	    saveOuter.push(pRoot);
	    var num = 0;
	    while(saveOuter.length)
	    {
	        var len = saveOuter.length;
	        var saveIner = [];
	        num++;
	        for(var i=0;i<len;i++)
	        {
	            var p = saveOuter.shift();
	            saveIner.push(p.val);
	            if(p.left!=null)
	                saveOuter.push(p.left);
	            if(p.right!=null)
	                saveOuter.push(p.right);
	        }
	        if(num % 2==0)
	            saveIner.reverse();
	        result.push(saveIner);
	    }
	    return result;
	}
	
	var a = new TreeNode(1);
	var b = new TreeNode(2);
	var c = new TreeNode(3);
	a.left = b;
	a.right =c;
	console.log(Print(a));
