（19.1.21）
# 剑指offer（22）从上往下打印二叉树

**从上往下打印出二叉树的每个节点，同层节点从左至右打印。**

用到了bfs的思想，将第一行存入queue的队列中，while循环此队列长度，还有就出队列，并把此元素的左右子树入队列，循环：


代码如下： 


	 function TreeNode(x) {
	    this.val = x;
	    this.left = null;
	    this.right = null;
	} 
	function PrintFromTopToBottom(root)
	{
	    if (!root) {
	        return [];
	    }
	    // write code here
	    let sck = [];
	    let list = [];
	    sck.push(root);
	    while(sck.length != 0)
	    {
	        let p = sck.shift();
	        if(p.left != null) {
	            sck.push(p.left);
	        }
	        if(p.right != null) {
	            sck.push(p.right);
	        }
	        list.push(p.val);
	    }
	    return list;
	}
	
	var a = new TreeNode(1);
	var b = new TreeNode(2);
	var c = new TreeNode(3);
	a.left = b;
	a.right = c;
	console.log(PrintFromTopToBottom(a));